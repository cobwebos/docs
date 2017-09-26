---
title: "计划程序高可用性和可靠性"
description: "计划程序高可用性和可靠性"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2016
ms.author: deli
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7e7fe49de7814b6058468d630f8638720e5864f3
ms.contentlocale: zh-cn
ms.lasthandoff: 12/08/2016

---
# <a name="scheduler-high-availability-and-reliability"></a>计划程序高可用性和可靠性
## <a name="azure-scheduler-high-availability"></a>Azure 计划程序高可用性
作为一个核心 Azure 平台服务，Azure 计划程序高度可用，并提供地域冗余服务部署和地理区域作业复制功能。

### <a name="geo-redundant-service-deployment"></a>异地冗余的服务部署
现在几乎可在 Azure 中的每个地理区域中通过 UI 使用Azure 计划程序。 Azure 计划程序可用的区域列表[在此处列出](https://azure.microsoft.com/regions/#services)。 如果托管区域中的某个数据中心服务中断，则 Azure 计划程序的故障转移功能便可发挥作用，使你能够从另一个数据中心使用该服务。

### <a name="geo-regional-job-replication"></a>地理区域作业复制
不只是 Azure 计划程序前端可用于管理请求，你自己的作业也会经过地域复制。 当一个区域中的服务中断时，Azure 计划程序将故障转移，并确保配对地理区域中的另一个数据中心运行作业。

例如，如果你已在美国中南部创建了一个作业，Azure 计划程序会自动在美国中北部复制该作业。 如果美国中南部发生故障，Azure 计划程序可确保从美国中北部运行该作业。 

![][1]

因此，在 Azure 发生故障时，Azure 计划程序可确保你的数据始终位于同样广泛的地理区域内。 你不需要复制作业而只是为了增加高可用性 – Azure 计划程序将自动为你的作业提供高可用性功能。

## <a name="azure-scheduler-reliability"></a>Azure 计划程序可靠性
Azure 计划程序可保证其自身的高可用性，并提供不同的途径访问用户创建的作业。 例如，你的工作可能会调用不可用的 HTTP 终结点。 尽管如此，Azure 计划程序会通过为你提供处理故障的替代选项，尝试成功执行你的作业。 Azure 计划程序通过两种方式实现此目的：

### <a name="configurable-retry-policy-via-retrypolicy"></a>通过“retryPolicy”实现的可配置重试策略
Azure 计划程序允许你配置重试策略。 默认情况下，如果某个作业失败，计划程序将在 30 秒的时间间隔内重试该作业四次。 你可以将此重试策略重新配置为更频繁（例如，在 30 秒时间间隔内重试 10 次）或更松散（例如，每天重试两次）。

举例来说，如果你创建的作业每周运行一次并调用 HTTP 终结点，则此配置可能会有帮助。 如果当你的作业运行时 HTTP 终结点已关闭了几个小时，则你可能不希望再等待一周来再次运行作业，因为默认重试策略将会失败。 在这种情况下，你可以重新配置标准的重试策略，以便每隔三个小时（举例）而不是每隔 30 秒重试。

若要了解如何配置重试策略，请参阅 [retryPolicy](scheduler-concepts-terms.md#retrypolicy)。

### <a name="alternate-endpoint-configurability-via-erroraction"></a>通过“errorAction”实现的备用终结点可配置性
如果 Azure 计划程序作业的目标终结点仍然无法访问，Azure 计划程序将回退到备用的错误处理终结点后重试策略。 如果配置了备用错误处理终结点，则 Azure 计划程序将调用该终结点。 有了备用终结点，在发生故障时你自己的作业将高度可用。

例如，在下图中，Azure 计划程序遵循其重试策略来访问位于纽约的 Web 服务。 重试失败后，它会检查是否有备用终结点。 然后，它将使用相同的重试策略继续向备用终结点发出请求。

![][2]

请注意，相同的重试策略适用于原始操作和备用错误操作。 备用错误操作的操作类型还可以不同于主要操作的操作类型。 例如，虽然主要操作可能调用 HTTP 终结点，但错误操作可能是执行错误日志记录的存储队列、服务总线队列或服务总线主题操作。

若要了解如何配置备用终结点，请参阅 [errorAction](scheduler-concepts-terms.md#action-and-erroraction)。

## <a name="see-also"></a>另请参阅
 [计划程序是什么？](scheduler-intro.md)

 [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)

 [开始在 Azure 门户中使用计划程序](scheduler-get-started-portal.md)

 [Azure 计划程序中的计划和计费](scheduler-plans-billing.md)

 [如何使用 Azure 计划程序生成复杂的计划和高级循环](scheduler-advanced-complexity.md)

 [Azure 计划程序 REST API 参考](https://msdn.microsoft.com/library/mt629143)

 [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)

 [Azure 计划程序的限制、默认值和错误代码](scheduler-limits-defaults-errors.md)

 [Azure 计划程序出站身份验证](scheduler-outbound-authentication.md)

[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png

