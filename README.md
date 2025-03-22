# Introduction to Apache Kafka.
Apache Kafka is an open-source distributed event streaming platform used for building real-time data pipelines and streaming applications. It is designed to handle high throughput, low-latency data streams, and is capable of processing large volumes of data in real-time.

Kafka works by publishing and subscribing to streams of records, similar to a messaging system. It allows for the storing, processing, and analysis of streams of data. It is highly scalable and fault-tolerant, making it popular for systems that require quick and reliable data movement.

Kafka operates based on a publish-subscribe model, where data is produced by producers, stored in topics, and consumed by consumers.


# CORE COMPONENTS
Producer: The application that sends data (messages) into Kafka topics.

Consumer: The application that reads data from Kafka topics.

Topic: A category or feed name to which records are sent. Each topic can have multiple partitions for scalability.

Broker: A Kafka server that stores data and serves clients.

ZooKeeper: A tool used to manage and coordinate Kafka brokers. (Note: Recent versions of Kafka are moving towards removing ZooKeeper, using KRaft mode instead.

# Problem Statement for Kafka
In modern data-driven systems, there is a need to efficiently manage, process, and analyze large amounts of real-time data that is constantly being generated from various sources. Traditional approaches to data processing and messaging (e.g., relational databases, monolithic architectures, and point-to-point messaging systems) often struggle to handle the scale, complexity, and real-time nature of modern data.

   Scalability Challenges: Traditional messaging systems often have limited scalability when it comes to handling millions or even billions of events per second across distributed systems. As the system grows, it becomes difficult to efficiently distribute, store, and process data at scale.

   Data Loss and Fault Tolerance: In traditional messaging systems, there’s a risk of data loss in case of system failures. Ensuring data durability and fault tolerance across different services and consumers becomes complex, especially as the volume of data increases.

   Latency and Real-Time Processing: In many business use cases, real-time event processing is critical. Examples include monitoring user activity, fraud detection, or responding to sensor data. Traditional systems can introduce significant delays in data processing, which is unsuitable for applications that require immediate action.

   Data Integration Across Multiple Systems: Modern applications often require data to be streamed and processed across a variety of services and systems. Ensuring that these systems can communicate in real time and handle data efficiently is challenging, especially when dealing with different data formats, protocols, or systems that may be geographically distributed.

   # How Kafka Solves These Problems:
   Scalability: Kafka is designed for high-throughput, horizontal scalability. By distributing data across many servers (brokers), Kafka can handle millions of messages per second and easily scale as more consumers or producers are added.

Durability and Fault Tolerance: Kafka provides strong guarantees for data durability and fault tolerance. Data is replicated across multiple brokers, ensuring that messages are not lost, even if individual servers fail.

Real-Time Processing: Kafka provides low-latency data streaming and is optimized for real-time applications. It allows consumers to process data as soon as it’s produced, ensuring immediate actions can be taken (e.g., triggering real-time analytics or notifications).

Data Integration: Kafka allows seamless integration across different services and systems. By using Kafka topics, data can be pushed to or pulled from multiple producers and consumers, regardless of the underlying system. Kafka supports diverse data sources (e.g., IoT devices, logs, databases) and helps to decouple microservices, enabling real-time communication between distributed applications.

# Kafka Architecture Components:
1.Producer:

 A Producer is an application or service that sends data to Kafka topics. It publishes messages (events, data, logs) to specific topics in Kafka.
 Producers are responsible for serializing data and determining which partition to send data to (either by using a specific partitioning strategy or Kafka’s default round-robin mechanism).
Example: A user activity tracking system where every user action (e.g., a click or login) is sent to Kafka by the producer.

2.Consumer:

 A Consumer is an application or service that reads data (messages) from Kafka topics. Consumers can subscribe to one or more topics and read data from Kafka in a way that is parallelized and scalable.
 Consumers can be grouped into consumer groups, where each consumer within the group reads data from different partitions of the same topic.
Example: A recommendation engine that consumes user interaction data to analyze and make personalized suggestions.

3.Brokers:

 Brokers are Kafka servers that store and manage data. A Kafka cluster consists of one or more brokers. Each broker is responsible for storing data and serving consumer requests.
 A single broker can handle multiple topics and partitions. The brokers are designed to be highly scalable and fault-tolerant.
 Brokers store data in partitions, and each partition is replicated across different brokers to ensure durability and fault tolerance.
Example: A Kafka cluster can have three brokers, each storing a portion of the data from various topics.

4.ZooKeeper (Used in Older Versions):

 ZooKeeper is an external service used by Kafka for managing cluster metadata and leader election. It tracks the Kafka brokers and helps coordinate Kafka brokers and topics.
ZooKeeper handles tasks like keeping track of which broker is the leader for a particular partition, which broker is up and running, and managing configuration changes.
Note: As of newer Kafka versions (KRaft mode), Kafka is transitioning away from using ZooKeeper, simplifying the architecture.


![image](https://github.com/user-attachments/assets/94ac83f2-c996-44b8-967a-0a4da2f2a8ed)


# Key Characteristics of a Consumer Group:
  Parallel Data Consumption:
  
Within a consumer group, each consumer is responsible for consuming data from a specific set of partitions of the topic(s) it is subscribing to.
his allows for parallel processing of messages from multiple partitions, enabling efficient consumption and processing of large-scale data.

   Load Balancing:
   
 Kafka automatically distributes partitions among consumers in a group. Each partition in a topic is consumed by exactly one consumer within a group at a time.
  If there are more partitions than consumers, some consumers will consume from multiple partitions.
 If there are more consumers than partitions, some consumers will be idle and not consume any data.

   Message Delivery Semantics:

  Each message in a partition is consumed by only one consumer within the group, which means that message processing within a consumer group is one-to-one.
However, if there are multiple consumer groups reading from the same topic, each group gets a copy of the message. This allows for multiple independent processing applications to work with the same data stream.

  Fault Tolerance:

  Kafka ensures that if a consumer in a group fails (e.g., crashes or becomes unavailable), the remaining consumers can take over the partitions that were assigned to the failed consumer.
This enables high availability and fault tolerance for data processing.

How Consumer Groups Work:

Let’s break down the consumer group behavior with an example. Suppose you have a topic called orders with 6 partitions, and you have a consumer group named OrderProcessingGroup with 3 consumers. Here's how Kafka will handle the distribution:
Step-by-Step Consumer Group Operation:

   Topic with Partitions:
Topic orders has 6 partitions: p0, p1, p2, p3, p4, p5.

  Consumer Group:
 You have a consumer group called OrderProcessingGroup with 3 consumers: C1, C2, and C3.

  Partition Assignment:
Kafka automatically assigns each partition to one consumer within the group. Given that there are more partitions than consumers, some consumers will take multiple partitions.

  For example:

  C1 consumes from partitions p0 and p1.

   C2 consumes from partitions p2 and p3.

  C3 consumes from partitions p4 and p5.

  Processing Messages:

   Each consumer processes the messages within the partitions it has been assigned. So, C1 will read messages from p0 and p1, while C2 and C3 will do the same for their assigned partitions.
This ensures parallel consumption of the data and balances the load across consumers.

  Fault Tolerance:

   If C2 crashes, Kafka will reassign the partitions p2 and p3 to the remaining consumers. In this case, C1 or C3 will take over those partitions, ensuring there’s no downtime in message consumption.

  Rebalancing:

 If a new consumer joins the group (e.g., C4), Kafka will trigger a rebalance, redistributing partitions among all consumers in the group. In this case, C4 may get assigned some of the partitions, and Kafka will ensure that each partition is only assigned to one consumer at a time.


 ![image](https://github.com/user-attachments/assets/5dadd6d9-8ea7-4aab-ba84-c79eec3b2352)



 #Prerequisite:
1. Knowledge
Node.JS Intermediate level
Experience with designing distributed systems

2.Tools
Node.js: Download Node.JS
Docker: Download Docker
VsCode: Download VSCode

 # Commands:
 Start Zookeper Container and expose PORT 2181.
# docker run -p 2181:2181 zookeeper

# Code

1.client.js

const { Kafka } = require("kafkajs");

exports.kafka = new Kafka({
  clientId: "my-app",
  brokers: ["<PRIVATE_IP>:9092"],
});

2.admin.js

const { kafka } = require("./client");

async function init() {
  const admin = kafka.admin();
  console.log("Admin connecting...");
  admin.connect();
  console.log("Adming Connection Success...");

  console.log("Creating Topic [rider-updates]");
  await admin.createTopics({
    topics: [
      {
      topic: "rider-updates",
        numPartitions: 2,
      },
    ],
  });
  console.log("Topic Created Success [rider-updates]");

  console.log("Disconnecting Admin..");
  await admin.disconnect();
}

init();

3.producer.js

const { kafka } = require("./client");
const readline = require("readline");

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
});

async function init() {
  const producer = kafka.producer();

  console.log("Connecting Producer");
  await producer.connect();
  console.log("Producer Connected Successfully");

  rl.setPrompt("> ");
  rl.prompt();

  rl.on("line", async function (line) {
    const [riderName, location] = line.split(" ");
    await producer.send({
      topic: "rider-updates",
      messages: [
        {
          partition: location.toLowerCase() === "north" ? 0 : 1,
          key: "location-update",
          value: JSON.stringify({ name: riderName, location }),
        },
      ],
    });
  }).on("close", async () => {
    await producer.disconnect();
  });
}

init();

4.consumer.js
const { kafka } = require("./client");
const group = process.argv[2];

async function init() {
  const consumer = kafka.consumer({ groupId: group });
  await consumer.connect();

  await consumer.subscribe({ topics: ["rider-updates"], fromBeginning: true });

  await consumer.run({
    eachMessage: async ({ topic, partition, message, heartbeat, pause }) => {
      console.log(
     `${group}: [${topic}]: PART:${partition}:`,
        message.value.toString()
      );
    },
  });
}

init();

# Runing locally

Run multiple consumer
node consumer.js <GROUP_NAME>

# Create Producer
node producer.js

> tony north
> tony south


