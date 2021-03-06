aws-kinesis-scala
========

Scala client for Amazon Kinesis.
Supports [Apache Spark](#apache-spark) write data to Kinesis (an input DStream using the KCL supports by Apache Spark).

## Installation

Add a following dependency into your `build.sbt`.

core only:
```scala
libraryDependencies += "jp.co.bizreach" %% "aws-kinesis-scala" % "0.0.2"
```

use spark integration:
```scala
libraryDependencies += "jp.co.bizreach" %% "aws-kinesis-spark" % "0.0.2"
```

## Usage

Create the `AmazonKinesisClient` at first.

```scala
import jp.co.bizreach.kinesis._

implicit val region = Regions.AP_NORTHEAST_1

// use DefaultAWSCredentialsProviderChain
val client = AmazonKinesisClient()

// specify an explicit Provider
val client = AmazonKinesisClient(new InstanceProfileCredentialsProvider())

// specify an explicit client configuration
val client = AmazonKinesisClient(new ClientConfiguration().withProxyHost("proxyHost"))

// both
val client = AmazonKinesisClient(
  new InstanceProfileCredentialsProvider(),
  new ClientConfiguration().withProxyHost("proxyHost")
)
```

Then you can access Kinesis as following:

```scala
val request = PutRecordRequest(
  streamName   = "streamName",
  partitionKey = "partitionKey",
  data         = "data".getBytes("UTF-8")
)

// not retry
client.putRecord(request)

// if failure, max retry count is 3 (SDK default)
client.putRecordWithRetry(request)
```

## [Apache Spark][]

aws-kinesis-spark provides integration with Spark: for writing, methods that work on any `RDD`.

Import the `jp.co.bizreach.kinesis.spark._` to gain `saveToKinesis` method on your RDDs:

```scala
import jp.co.bizreach.kinesis.spark._

val rdd: RDD[Map[String, Option[Any]]] = ...

rdd.saveToKinesis(
  streamName = "streamName",
  region     = Regions.AP_NORTHEAST_1,
  chunk      = 30
)
```

[Apache Spark]: http://spark.apache.org
