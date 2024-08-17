# Circuit Breaker

The Circuit Breaker pattern is used to detect and handle faults in a distributed system to prevent cascading failures. It acts as a proxy for operations that might fail, allowing the system to gracefully handle failures and recover when the faults are resolved.

## Use cases

- External API calls: protect service from failing third-party APIs by wrapping the API calls with a circuit breaker.
- Microservices communication: prevent cascading failures in microservice architectures by implementing circuit breakers for inter-service calls.

## Benefits

- Graceful degradation: helps the system to fail gracefully, providing fallback options or error messages to the users instead of leaving them waiting.
- Fault isolation: prevents a single failing component from bringing down the entire system by stopping the propagation of errors.
- Quick recovery: allows the system to quickly recover from temporary issues by periodically allowing test requests.
- Resources utilization: reduces resources tied up in operations which are likely to fail.

## States

- Closed state: in this state, the circuit breaker allows all requests to pass through. If the number of consecutive failures exceeds a certain threshold, the circuit breaker transitions to the open state.
- Open state: in this state, the circuit breaker short-circuits and immediately fails all requests without attempting to execute them. After a specified timeout period, the circuit breaker transitions to the half-open state.
- Half-open state: in this state, the circuit breaker allows a limited number of test requests to pass through. If these requests succeed, the circuit breaker transitions back to the closed state. If they fail, it transitions back to the open state.

## Considerations

- Thresholds and timeout configurations: configure the thresholds and timeout values based on the expected load and failure scenarios.
- Fallback mechanisms: provide alternative responses or fallback mechanisms when the circuit breaker is open to ensure a good user experience (maybe returning stale data from cache if the invocation failed to return a response?).

## Example

```python
import time
from typing import Callable, Any


class CircuitBreaker:
  class BreakerMonitor:
    def alert(self, message):
      print(f"Alert: {message}")

  class Open(Exception):
    pass

  def __init__(self, func: Callable):
    self.func = func
    self.invocation_timeout = 0.01
    self.failure_threshold = 5
    self.monitor = CircuitBreaker.BreakerMonitor()
    self.reset_timeout = 0.1
    self.reset()

  def call(self, args: Any):
    if self.state() in ['closed', 'half_open']:
      try:
        return self.do_call(args)
      except Exception:
        self.record_failure()
        raise
    elif self.state() == 'open':
      raise CircuitBreaker.Open()
    else:
      raise Exception("Unreachable")

  def state(self):
    if (self.failure_count >= self.failure_threshold) and \
      (time.time() - self.last_failure_time) > self.reset_timeout:
      return 'half_open'
    elif self.failure_count >= self.failure_threshold:
      return 'open'
    else:
      return 'closed'

  def record_failure(self):
    self.failure_count += 1
    self.last_failure_time = time.time()
    
    if self.state() == 'open':
      self.monitor.alert('open_circuit')

  def reset(self):
    self.failure_count = 0
    self.last_failure_time = None
    
    self.monitor.alert('reset_circuit')

  def do_call(self, args: Any):
    # the invocation logic goes here, including timeout handling (raising exception if invocation time exceeds self.invocation_timeout)
    pass
```

## References

- [Pattern: Circuit Breaker](https://microservices.io/patterns/reliability/circuit-breaker.html)
- [Circuit Breaker by Martin Fowler](https://martinfowler.com/bliki/CircuitBreaker.html)
- [Circuit Breaker pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker)
- [The Circuit Breaker Pattern | Resilient Microservices](https://www.youtube.com/watch?v=5_Bt_OEg0no&t=12s&ab_channel=NickChapsas)
- [Circuit Breaker Pattern (Design Patterns for Microservices)](https://medium.com/geekculture/design-patterns-for-microservices-circuit-breaker-pattern-276249ffab33)
- [Cascading failure](https://en.wikipedia.org/wiki/Cascading_failure)
- [Making the Netflix API More Resilient](https://netflixtechblog.com/making-the-netflix-api-more-resilient-a8ec62159c2d)
- [Fault Tolerance in a High Volume, Distributed System](https://netflixtechblog.com/fault-tolerance-in-a-high-volume-distributed-system-91ab4faae74a)
