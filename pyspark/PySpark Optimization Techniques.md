source: https://medium.com/@sounder.rahul/pyspark-optimization-techniques-for-data-engineers-df5033778709

## Use Broadcast Variables
- read-only, shared variables that are cached on each executor in a Spark cluster, created using the `spark.sparkContext.broadcast()` method
- allows to efficiently distribute data to all worker nodes

### Key Methods for Broadcast Variables
- `spark.sparkContext.broadcast(value)` - creates a broadcast variable; distributes the value to all executors
- `.value` - accesses the broadcast variable's value; retrieves the cached data on each node
- `.unpersist()` - removes the broadcast variable from executor memory; frees resources when no longer needed
- `.destroy()` - completely destroys the broadcast variable; ensures it's removed from all nodes

## Partitioning
Different types of partitions:
1. Hash Partitioning
2. Range Partitioning
3. Using `partitionBy`