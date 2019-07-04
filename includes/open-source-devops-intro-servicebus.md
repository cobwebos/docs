---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173640"
---
[Azure 服务总线](/azure/service-bus-messaging/service-bus-messaging-overview)是企业级[集成](https://azure.microsoft.com/product-categories/integration/)消息中转站。 服务总线支持两种类型的通信：队列和主题。 

队列支持应用程序之间的异步通信。 应用将消息发送到队列，队列存储消息。 接收应用程序随后连接到队列并从队列中读取消息。

主题支持发布-订阅模式，该模式实现消息发送方和消息接收方之间的一对多关系。