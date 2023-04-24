### What are Accumulators?
As the name hints, accumulators are variables that accumulate. Because Spark runs in distributed mode, the workers are running in parallel, but asynchronously. For example, **worker 1** will not be able to know how far **worker 2** and **worker 3** are done with their tasks. With the same anology, the variables that are local to workers are not going to be shared to another worker unless you accumulate them. Accumulators are used for mostly `sum` operators, like in Hadoop MapReduce, but you can implement it to do otherwise.
For additional deep-dive, here is the [Spark documentation on accumulators](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#accumulators) if you want to learn more about these.

> What would be the best scenario for using Spark Accumlatiors? 
>- [ ] When you're using transformation functions accross your code
>- [x] When your know you will have different values across your executors :tada:

### What is Spark Broadcast?
Spark Broadcast variable are `secured`, `read-only` variables that get distriburted and cached to worker nodes. This is helpfu to Sparl because when the driver sends the data and tasks attached together which could be a little heavier on the network side.
Broadcast variable seek to reduce network overhead and to reduce communications. Spark Broadcast variables are used only with Spark Context.

