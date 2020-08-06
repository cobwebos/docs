---
title: 包含文件
description: 包含文件
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 9030080d0b8c8e032cb2992a62275efcdb04aabc
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798131"
---
下表列出了 Azure 服务总线当前支持的 (JMS) 功能的 Java 消息服务。 它还显示不支持的功能。


| 功能 | API |状态 |
|---|---|---|
| 队列   | <ul> <li> JMSContext. createQueue ( 字符串 queueName)  </li> </ul>| **支持** |
| 主题   | <ul> <li> JMSContext. createTopic ( 字符串 topicName)  </li> </ul>| **支持** |
| 临时队列 |<ul> <li> JMSContext. createTemporaryQueue ( # A1 </li> </ul>| **支持** |
| 临时主题 |<ul> <li> JMSContext. createTemporaryTopic ( # A1 </li> </ul>| **支持** |
| 消息生成者/<br/> JMSProducer |<ul> <li> JMSContext. createProducer ( # A1 </li> </ul>| **支持** |
| 队列浏览器 |<ul> <li> JMSContext. createBrowser (队列队列)  </li> <li> JMSContext. createBrowser (Queue queue，String messageSelector)  </li> </ul> | **支持** |
| 消息使用者/ <br/> JMSConsumer | <ul> <li> JMSContext. createConsumer ( 目标目标)  </li> <li> JMSContext. createConsumer ( 目标目标，字符串 messageSelector)  </li> <li> JMSContext. createConsumer ( 目标目标、String messageSelector、boolean noLocal) </li> </ul>  <br/> 当前不支持 noLocal | **支持** |
| 共享持久订阅 | <ul> <li> JMSContext. createSharedDurableConsumer (主题主题，String name)  </li> <li> JMSContext. createSharedDurableConsumer (主题主题，String name，String messageSelector)  </li> </ul>| **支持**|
| 非共享持久订阅 | <ul> <li> JMSContext. createDurableConsumer (主题主题，String name)  </li> <li> createDurableConsumer (主题主题，String name，String messageSelector，boolean noLocal)  </li> </ul> <br/> 当前不支持 noLocal，因此应将其设置为 false | **支持** |
| 共享的非持久订阅 |<ul> <li> JMSContext. createSharedConsumer (主题主题，String sharedSubscriptionName)  </li> <li> JMSContext. createSharedConsumer (主题主题，String sharedSubscriptionName，String messageSelector)  </li> </ul> | **支持** |
| 非持久订阅 |<ul> <li> JMSContext. createConsumer (目标目标)  </li> <li> JMSContext. createConsumer ( 目标目标，字符串 messageSelector)  </li> <li> JMSContext. createConsumer ( 目标目标、String messageSelector、boolean noLocal)  </li> </ul> <br/> 当前不支持 noLocal，因此应将其设置为 false | **支持** |
| 消息选择器 | 取决于创建的使用者 | **支持** |
| 计划的消息 (传递延迟)  | <ul> <li> JMSProducer. setDeliveryDelay ( long deliveryDelay)  </li> </ul>|**支持**|
| 已创建消息 |<ul> <li> JMSContext. createMessage ( # A1 </li> <li> JMSContext. createBytesMessage ( # A1 </li> <li> JMSContext. createMapMessage ( # A1 </li> <li> JMSContext. createObjectMessage ( Serializable 对象)  </li> <li> JMSContext. createStreamMessage ( # A1 </li> <li> JMSContext. createTextMessage ( # A1 </li> <li> JMSContext. createTextMessage ( 字符串文本)  </li> </ul>| **支持** |
| 分布式事务 || 不支持 |
