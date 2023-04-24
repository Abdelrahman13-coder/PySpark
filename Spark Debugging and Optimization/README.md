### What are Accumulators?
As the name hints, accumulators are variables that accumulate. Because Spark runs in distributed mode, the workers are running in parallel, but asynchronously. For example, **worker 1** will not be able to know how far **worker 2** and **worker 3** are done with their tasks. With the same anology, the variables that are local to workers are not going to be shared to another worker unless you accumulate them. Accumulators are used for mostly `sum` operators, like in Hadoop MapReduce, but you can implement it to do otherwise.
For additional deep-dive, here is the [Spark documentation on accumulators](https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#accumulators) if you want to learn more about these.

> What would be the best scenario for using Spark Accumlatiors? 
>- [ ] When you're using transformation functions accross your code
>- [x] When your know you will have different values across your executors :tada:

### What is Spark Broadcast? :shipit:
Spark Broadcast variable are `secured`, `read-only` variables that get distriburted and cached to worker nodes. This is helpfu to Sparl because when the driver sends the data and tasks attached together which could be a little heavier on the network side.
Broadcast variable seek to reduce network overhead and to reduce communications. Spark Broadcast variables are used only with Spark Context.

> When is broadcast usually used in Spark?
> - [x] Broadcast join is a way of joining a large table and small table in Spark.
> - [ ] Broadcast variable is cached variable in the driver.
> - [ ] Broadcast variable is shipped to each machine with tasks
> - [x] Broadcast join is like map-side join in MapReduce 

### Tips for Debugging Code Errors
Typos are probably the simplest errors to identify
* A <ins>typo</ins> is a method name will generate a <ins>short attribute error</ins>
* An <ins>incorrect column name</ins> will result in a <ins>long analysis exception error</ins>
* <ins>Typos in variable</ins> can result in <ins>lengthy errors</ins>
* While Spark supports the Python API, its native language is Scala. That's why some of the error messages are referring to Scala, Java, or JVM issues even when we are running Python code.
* Whenever you use `collect`,be careful how much data are you collecting
* <ins>Mismatched parantheses</ins> can cause end-of-file (EOF) errors that may be misleading

>You are running Spark code, and you encounter an error. What are some things you should look for in your code to resolve it?
> - [x] Typos in your method names
> - [ ] How long your Spark program is taking to run
> - [x] Mismatched parantheses
> - [x] Use of collect() in your code
