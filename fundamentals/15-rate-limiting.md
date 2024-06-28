# Rate Limiting

Rate limiting is a technique used to control the rate at which users or clients can make requests to a service

## Use cases

Rate limiting is an effective measure against several types of attacks and abuse, including:

- Denial of Service (DoS) attacks: limiting the number of requests a client can make.
- Brute force attacks: it helps in preventing attackers from trying multiple combinations of credentials or keys in a short time.
- Web scraping: it reduces the effectiveness of automated scripts that scrape data from a website.
- Inventory hoarding attacks: limiting the number of requests for a particular item or resource can thwart inventory hoarding attacks, where attackers try to purchase all available inventory of a popular item to resell at a higher price.

## Types

- User-based rate limiting: limits the number of requests a single user can make in a given timeframe.
- IP-based rate limiting: limits the number of requests from a particular IP address.
- Geographical rate limiting: limits the number of requests based on the geographical location of the request.
- Service/endpoint-based rate limiting: limits the number of requests to a particular service or API endpoint.

## Algorithms

### Fixed window counter algorithm

Limits the number of requests received within a fixed time window, such as one minute. Once the maximum number of requests is reached, additional requests are rejected until the next window begins.

Pros: simple to implement.
Cons: can lead to uneven traffic distribution (e.g., bursts at the start of each window).

Steps:

1. Determine the current window based on the current time and window size.
2. Generate a key for the current window.
3. Check the count of requests for the current window in Redis.
4. If the count is within the limit, increment the count and allow the request.
5. If the count exceeds the limit, deny the request.

```python
import time

def fixed_window_rate_limit(user_id, max_requests, window_size):
  current_time = int(time.time())
  current_window = current_time // window_size
  key = f"{user_id}:{current_window}"

  current_count = redis.get(key)

  if current_count is None:
    redis.set(key, 1, ex=window_size)
    return True
  elif int(current_count) < max_requests:
    redis.incr(key)
    return True
  else:
    return False
```

### Sliding window counter algorithm

Similar to the fixed window algorithm, but the window moves forward in time, providing a more granular and even distribution of requests.

Pros: more accurate rate limiting compared to fixed window.
Cons: slightly more complex to implement.

Steps:

1. Determine the current time in milliseconds.
2. Generate keys for the current and previous intervals.
3. Remove expired timestamps from Redis.
4. Count the number of requests in the current window.
5. If the count is within the limit, add the current timestamp and allow the request.
6. If the count exceeds the limit, deny the request

```python
def sliding_window_rate_limit(user_id, max_requests, window_size):
  current_time = int(time.time() * 1000)
  key = f"{user_id}:timestamps"

  # Remove timestamps that are older than the window_size
  redis_client.zremrangebyscore(key, 0, current_time - window_size)

  current_count = redis_client.zcard(key)
  
  if current_count < max_requests:
    redis_client.zadd(key, {current_time: current_time})
    redis_client.expire(key, window_size // 1000)  # Expire the key after the window size in seconds
    return True
  else:
    return False
```

### Token bucket algorithm

A bucket holds a fixed number of tokens, and each request consumes a token. Tokens are added to the bucket at a fixed rate.

Pros: allows bursts of traffic while maintaining a steady rate of requests over time.
Cons: requires careful tuning of token addition rate and bucket size.

Steps:

1. Maintains a count of tokens and the last refill timestamp.
2. Adds new tokens based on the elapsed time since the last refill.
3. If tokens are available, it consumes one and updates the token count and timestamp.
4. Denies the request if no tokens are available.

```python
import time
import math

def token_bucket_rate_limit(user_id, max_tokens, refill_time, refill_amount, window_size):
  key_tokens = f"{user_id}:tokens"
  key_timestamp = f"{user_id}:last_refill_timestamp"

  current_time = int(time.time())
  current_tokens = redis.get(key_tokens)
  last_refill_timestamp = redis.get(key_timestamp)

  current_tokens = int(current_tokens) if current_tokens else max_tokens
  last_refill_timestamp = int(last_refill_timestamp) if last_refill_timestamp else current_time

  refill_count = math.floor((current_time - last_refill_timestamp) / refill_time)

  new_tokens = min(max_tokens, current_tokens + (refill_count * refill_amount))
  new_refill_timestamp = min(current_time, last_refill_timestamp + (refill_count * refill_time))

  if new_tokens > 0:
    redis.set(key_tokens, new_tokens - 1, ex=window_size)
    redis.set(key_timestamp, new_refill_timestamp, ex=window_size)
    return True
  else:
    return False
```

### Leaky bucket algorithm

Similar to the token bucket algorithm, but requests enter a bucket at any rate, but they are processed (or leaked) at a fixed rate.

If the rate at which requests arrive in the bucket is greater than the rate at which requests are processed (leaked from the bucket), the bucket will fill up and further requests will be dropped until there is space in the bucket.

Pros: smoothens out burst traffic, ensuring a consistent and controlled rate of processing.
Cons: may introduce latency if bursts are large.

Steps:

1. Tracks the number of requests and the last checked timestamp.
2. Calculates the number of leaked requests based on the elapsed time and leak rate.
3. Updates the request count by subtracting the leaked requests.
4. Adds a new request if within the limit, otherwise denies the request.

```python
def leaky_bucket_rate_limit(user_id, max_requests, leak_rate, window_size):
  key_requests = f"{user_id}:requests"
  key_timestamp = f"{user_id}:last_checked_timestamp"

  current_time = int(time.time())
  current_requests = redis.get(key_requests)
  last_checked_timestamp = redis.get(key_timestamp)

  current_requests = int(current_requests) if current_requests else 0
  last_checked_timestamp = int(last_checked_timestamp) if last_checked_timestamp else current_time
  
  elapsed_time = current_time - last_checked_timestamp
  leaked_requests = elapsed_time * leak_rate
  new_requests = max(0, current_requests - leaked_requests)

  if new_requests < max_requests:
    redis.set(key_requests, new_requests + 1, ex=window_size)
    redis.set(key_timestamp, current_time, ex=window_size)
    return True
  else:
    return False
```

## External references

- [Rate Limiting Defined](https://redis.io/glossary/rate-limiting/)
- [Distributed API Rate Limiter](https://systemsdesign.cloud/SystemDesign/RateLimiter)
- [Why, where, and when should we throttle or rate limit?](https://www.youtube.com/watch?v=CW4gVlU0xtU&ab_channel=ArpitBhayani)
