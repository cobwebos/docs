---
title: Azure 队列简介 - Azure 存储
description: 查看 Azure 队列简介，它是一项可存储大量消息的服务。 队列服务包括 URL 格式、存储帐户、队列和消息。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2020
ms.service: storage
ms.subservice: queues
ms.topic: overview
ms.reviewer: dineshm
ms.openlocfilehash: c16f02922b2e815ddd4986ab07bf260b20d871ee
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826494"
---
# <a name="what-are-azure-queues"></a>什么是 Azure 队列？

Azure 队列存储是一项可存储大量消息的服务。 可以使用 HTTP 或 HTTPS 通过经验证的调用从世界任何位置访问消息。 队列消息大小最大可为 64 KB。 一个队列可以包含数百万条消息，直至达到存储帐户的总容量限值。 队列通常用于创建要异步处理的积压工作 (backlog)。

## <a name="queue-service-concepts"></a>队列服务概念

队列服务包含以下组件：

![显示存储帐户、队列和消息之间关系的示意图](./media/storage-queues-introduction/queue1.png)

* **URL 格式：** 可使用以下 URL 格式对队列进行寻址：

    `https://<storage account>.queue.core.windows.net/<queue>`
  
    可使用以下 URL 访问示意图中的某个队列：  
  
    `https://myaccount.queue.core.windows.net/images-to-download`

* **存储帐户：** 对 Azure 存储进行的所有访问都要通过存储帐户完成。 有关存储帐户容量的信息，请参阅[标准存储帐户的可伸缩性和性能目标](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。

* **队列：** 一个队列包含一组消息。 队列名称**必须**全部小写。 有关命名队列的信息，请参阅 [命名队列和元数据](https://msdn.microsoft.com/library/azure/dd179349.aspx)。

* **消息：** 一条消息（无论哪种格式）的最大大小为 64 KB。 在 2017-07-29 以前的版本中，允许的最大生存时间为 7 天。 在 2017-07-29 或更高版本中，最大生存时间可以是任何正数，或者是 -1（表示消息不会过期）。 如果省略此参数，则默认的生存时间为 7 天。

## <a name="next-steps"></a>后续步骤

* [创建存储帐户](../storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [使用 .NET 的队列入门](storage-dotnet-how-to-use-queues.md)
