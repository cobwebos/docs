---
title: 设计相同输入的 Azure Functions
description: 构建 Azure Functions 是幂等的
author: craigshoemaker
ms.author: cshoe
ms.date: 9/12/2019
ms.topic: article
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 39e785a1ca7a158ddb90a3e6ba914582c405612a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997388"
---
# <a name="designing-azure-functions-for-identical-input"></a>设计相同输入的 Azure Functions

事件驱动的体系结构和基于消息的体系结构的现实要求接受相同的请求，同时保持数据完整性和系统稳定性。

要说明，请考虑使用电梯调用按钮。 按下该按钮时，它会亮起，并向地面发送电梯。 几分钟后，其他人可以在大厅中加入您。 此人微笑你，然后再次按下 "发光" 按钮。 当你提醒该命令调用电梯时，你会想起并 chuckle 给你自己。

按下电梯调用按钮的第二、第三次或第四次时间与最终结果无关。 按下该按钮时，不管发生了多少次，电梯都将发送到地面。 与电梯一样，幂等系统会导致相同的结果，无论发出的命令有多少次。

在构建应用程序时，请考虑以下方案：

- 如果库存控制应用程序多次尝试删除同一产品，会发生什么情况？
- 如果有多个请求为同一人员创建员工记录，则人力资源应用程序如何运行？
- 如果银行应用收到100请求来进行相同的提款，会发生什么？

在许多情况下，对函数的请求可能会接收到相同的命令。 某些情况包括：

- 多次发送同一请求的重试策略
- 已将缓存的命令重播到应用程序
- 发送多个相同请求时出现应用程序错误

为了保护数据完整性和系统运行状况，幂等应用程序包含可能包含以下行为的逻辑：

- 在尝试执行删除之前验证是否存在数据
- 在尝试执行 "创建" 操作之前，检查是否已存在数据
- 协调在数据中创建最终一致性的逻辑
- 并发控制
- 重复检测
- 数据刷新验证
- 保护验证输入数据的逻辑

最终幂等性是通过确保给定的操作是可以实现的，并且只执行一次。
