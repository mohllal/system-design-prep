# MapReduce

MapReduce is a programming model and processing technique for distributed computing on large datasets.

A MapReduce program is composed of a map function that processes a key/value pair to generate a set of intermediate key/value pairs, and a reduce function that merges all intermediate values associated with the same intermediate key.

## Use Cases

- Processing and analyzing server logs.
- Processing user behavior across website.
- Indexing web pages and ranking them.

## Concepts

- Map function: processes input data and produces a set of intermediate key-value pairs.
- Reduce function: merges all intermediate values associated with the same intermediate key.

Data Flow: Input data -> Map function -> Shuffle and Sort -> Reduce function -> Output data.

## Flow

1. Input splitting: input data is split into fixed-size chunks.
2. Mapping: each chunk is processed by a mapper, which outputs key-value pairs.
3. Shuffling and sorting: intermediate key-value pairs are shuffled and sorted by key.
4. Reducing: reducers process sorted key-value pairs to produce final output.
5. Output: final results are written to the distributed storage (materialized view).

## Fault tolerance and scalability

Fault tolerance is achieved in MapReduce by:

- Task retries: if a task fails, it is retried on another machine.
- Data replication: input data and intermediate results are replicated across multiple nodes.

Scalability is is achieved in MapReduce by:

- Horizontal scaling: add more nodes to handle larger datasets and distribute the processing load.
- Data locality: processing tasks are moved to where the data resides to minimize data transfer.

## Example

An example of a program that counts the number of occurrences of each unique word in a set of input files specified on the command line.
Writing to the mapper result to the multiple files and reading it back from it is trimmed for simplicity.

```python
import sys
import re
from collections import defaultdict

# Mapper function
def mapper(text):
  word_count = defaultdict(int)
  words = re.findall(r'\w+', text.lower())
  for word in words:
      word_count[word] += 1
  return word_count.items()

# Shuffle and sort (group by key)
def shuffle_and_sort(mapped_items):
    shuffled = defaultdict(list)
    for key, value in mapped_items:
        shuffled[key].append(value)
    return sorted(shuffled.items())

# Reducer function
def reducer(word_counts):
  word_count = defaultdict(int)
  for word, count in word_counts:
    for count in counts:
      word_count[word] += int(count)
  return word_count.items()

if __name__ == "__main__":
  if len(sys.argv) < 2:
      print("Usage: python mapreduce_example.py <input_file>")
      sys.exit(1)

  input_file = sys.argv[1]
  with open(input_file, 'r') as file:
      text = file.read()

  # Step 1: Map phase
  mapped_items = mapper(text)

  # Step 2: Shuffle and sort phase
  sorted_items = shuffle_and_sort(mapped_items)

  # Step 3: Reduce phase
  word_counts = reducer(sorted_items)

  # Output results
  for word, count in word_counts:
    print(f"{word}\t{count}")
```

## Technologies

- [Apache Hadoop](https://hadoop.apache.org/) - widely used open-source implementation of the MapReduce framework.
- [Apache Spark](https://spark.apache.org/) - provides in-memory processing and supports MapReduce-like operations.

## External references

- [MapReduce](https://www.databricks.com/glossary/mapreduce)
- [Everything you need to know about MapReduce](https://blog.det.life/everything-you-need-to-know-about-mapreduce-aff1c664f3b5)
- [MapReduce explained with example](https://levelup.gitconnected.com/map-reduce-explained-with-example-system-design-af7b868187a5)
- [What is MapReduce](https://www.youtube.com/watch?v=lHp7M078nHo&ab_channel=Jordanhasnolife)
