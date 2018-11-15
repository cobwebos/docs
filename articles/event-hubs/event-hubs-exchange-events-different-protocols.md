---
title: 在 Azure 事件中心内使用不同协议的使用者与生成者之间交换事件 | Microsoft Docs
description: 本文介绍使用不同协议（AMQP、Apache Kafka 和 HTTPS）的使用者和生成者在使用 Azure 事件中心时如何交换事件。
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2018
ms.author: bahariri
ms.openlocfilehash: 54edeafbe950cde88c2c6051f95825cfaed52ac7
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285309"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>在使用不同协议（AMQP、Kafka 和 HTTPS）的使用者与生成者之间交换事件
Azure 事件中心支持使用者和生成者的三种协议：AMQP、Kafka 和 HTTPS。 其中每个协议使用自身的方式来表示消息，因此，自然而然引出了以下问题：如果应用程序通过一种协议将事件发送到事件中心，并通过另一种协议来使用事件，那么，当事件抵达使用者时，事件的各个组成部分和值是怎样的？ 本文介绍有关生产者和使用者的最佳做法，确保使用方应用程序正确解释事件中的值。

具体而言，本文中的建议涵盖以下客户端（开发代码片段时使用了所列的版本）：

* Kafka Java 客户端 (版本 1.1.1： https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* 适用于 Java 的 Microsoft Azure 事件中心客户端 (版本 1.1.0： https://github.com/Azure/azure-event-hubs-java)
* 适用于 .NET 的 Microsoft Azure 事件中心客户端 (版本 2.1.0： https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure 服务总线 (版本 5.0.0： https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS（仅支持生成者）

其他 AMQP 客户端在行为上可能稍有不同。 AMQP 具有妥善定义的类型系统，但与该类型系统之间相互进行的语言特定类型序列化，以及客户端提供对 AMQP 消息组成部分的访问的方式，都取决于客户端。

## <a name="event-body"></a>事件正文
所有 Microsoft AMQP 客户端以未解释的字节包形式表示事件正文。 生成方应用程序将字节序列传递给客户端，使用方应用程序从客户端接收相同的序列。 字节序列的解释在应用程序代码内部发生。

通过 HTTPS 发送事件时，事件正文是通过 POST 发布的内容，也被视为未解释的字节。 使用提供的 ByteArraySerializer 和 ByteArrayDeserializer，可在 Kafka 生成者或使用者中轻松实现相同的状态，如以下代码中所示：

### <a name="kafka-byte-producer"></a>Kafka byte[] 生成者

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte[] 使用者
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

此代码在应用程序的两个部分之间创建透明字节管道，并允许应用程序开发人员以所需的任意方式手动序列化和反序列化，例如，根据事件的用户设置属性中的类型或发送方信息，在运行时做出反序列化决策。

具有单个固定事件正文类型的应用程序有时可以使用其他 Kafka 序列化程序和反序列化程序以透明方式转换数据。 例如，假设某个应用程序使用 JSON。 JSON 字符串的构造和解释在应用程序级别发生。 在事件中心级别，事件正文始终是一个字符串，即，以 UTF-8 编码格式表示字符的字节序列。 在这种情况下，Kafka 生成者或使用者可以利用提供的 StringSerializer 或 StringDeserializer，如以下代码中所示：

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 字符串生成者
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 字符串使用者
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

在 AMQP 端，Java 和 .NET 提供内置的方法用于在字符串与 UTF-8 字节序列之间相互转换。 Microsoft AMQP 客户端以名为 EventData 的类表示事件。 以下示例演示如何将 UTF-8 字符串序列化成 AMQP 生成者中的 EventData 事件正文，以及如何将 EventData 事件正文反序列化成 AMQP 使用者中的 UTF-8 字符串。

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 字符串生成者
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 字符串使用者
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 字符串生成者
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 字符串使用者
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

由于 Kafka 是开源协议，应用程序开发人员可以检查任何序列化程序或反序列化程序的实现，并在 AMQP 端实现代码来生成或使用兼容的字节序列。

## <a name="event-user-properties"></a>事件用户属性

可以从 AMQP 客户端与 Kafka 设置和检索用户设置的属性。在 Microsoft AMQP 客户端中，这些属性称为属性；在 Kafka 中，它们称为标头。 HTTPS 发送方可以通过在 POST 操作中以 HTTP 标头形式提供用户属性，在事件中设置这些属性。 但是，Kafka 将事件正文和事件标头值都视为字节序列。 而在 AMQP 客户端中，属性值具有类型，这些类型是根据 AMQP 类型系统通过编码属性值来传递的。

HTTPS 的情况比较特殊。 发送时，所有属性值都是 UTF-8 文本。 事件中心服务执行有限数量的解释，将相应的属性值转换为 AMQP 编码的 32 位和 64 位带符号整数、64 位浮点数和布尔值。 不适合其中某个类型的任何属性值被视为字符串。

混合使用这些方法进行属性类型化将意味着，Kafka 使用者会看到原始的 AMQP 编码字节序列，包括 AMQP 类型信息。 而 AMQP 使用者会看到 Kafka 生成者发送的、应用程序必须解释的非类型化字节序列。

对于从 AMQP 或 HTTPS 生成者接收属性的 Kafka 使用者，请使用 AmqpDeserializer 类，该类在 Kafka 生态系统中的其他反序列化程序之后建模。 该类会解释 AMQP 编码字节序列中的类型信息，以将数据字节反序列化成 Java 类型。

作为最佳做法，我们建议在通过 AMQP 或 HTTPS 发送的消息中包含一个属性。 Kafka 使用者可以使用该属性来确定标头值是否需要 AMQP 反序列化。 该属性的值并不重要。 它只需是 Kafka 使用者可在标头列表中找到的，并可相应调整其行为的已知名称。

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP 到 Kafka 第 1 部分：在 C# (.NET) 中创建并发送包含属性的事件
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP 到 Kafka 第 2 部分：使用 AmqpDeserializer 在 Kafka 使用者中反序列化这些属性
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

如果应用程序知道属性的预期类型，则某些反序列化方法以后不需要强制转换，但在该属性不是预期类型时会引发错误。

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP 到 Kafka 第 3 部分：在 Kafka 使用者中使用 AmqpDeserializer 的不同方式
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

另一个方向的交换更复杂，因为 Kafka 生成者设置的标头始终被 AMQP 使用者视为原始字节（适用于 Java 的 Microsoft Azure 事件中心客户端的类型 org.apache.qpid.proton.amqp.Binary，或 Microsoft .NET AMQP 客户端的 `System.Byte[]`）。 最简单的途径是使用 Kafka 提供的某个序列化程序在 Kafka 生成者端生成标头值的字节，然后在 AMQP 使用者端编写兼容的反序列化代码。

与 AMQP 到 Kafka 的交换一样，建议的最佳做法是在通过 Kafka 发送的消息中包含一个属性。 AMQP 使用者可以使用该属性来确定标头值是否需要反序列化。 该属性的值并不重要。 它只需是 AMQP 使用者可在标头列表中找到的，并可相应调整其行为的已知名称。 如果无法更改 Kafka 生成者，使用方应用程序也可以检查属性值是二进制还是字节类型，并尝试基于该类型进行反序列化。

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka 到 AMQP 第 1 部分：从 Kafka 创建并发送包含属性的事件
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka 到 AMQP 第 2 部分：在 C# (.NET) 中手动反序列化这些属性
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka 到 AMQP 第 3 部分：在 Java 中手动反序列化这些属性
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>后续步骤
本文介绍了如何在不更改协议客户端或运行自己的群集的情况下，流式传输到已启用 Kafka 的事件中心。 若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

* [了解事件中心](event-hubs-what-is-event-hubs.md)
* [了解适用于 Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)
* [在适用于 Kafka 的事件中心 GitHub 上浏览更多示例](https://github.com/Azure/azure-event-hubs-for-kafka)
* 使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) [将事件从本地 Kafka 流式传输到云端已启用 Kafka 的事件中心。](event-hubs-kafka-mirror-maker-tutorial.md)
* 了解如何使用[本机 Kafka 应用程序](event-hubs-quickstart-kafka-enabled-event-hubs.md)、[Apache Flink](event-hubs-kafka-flink-tutorial.md) 或 [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) 流式传输到已启用 Kafka 的事件中心
