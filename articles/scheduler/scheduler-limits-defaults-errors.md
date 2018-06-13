---
title: 计划程序限制和默认值
description: 计划程序限制和默认值
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23043162"
---
# <a name="scheduler-limits-and-defaults"></a>计划程序限制和默认值
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>计划程序配额、限制、默认值和中止值
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>x-ms-request-id 标头
对计划程序服务的每个请求都会返回一个名为 **x-ms-request-id** 的响应标头。此响应标头包含一个唯一标识此请求的不透明值。

如果请求总是失败，且已验证请求格式正确，可以使用此值向 Microsoft 报告错误。 在报表中包含 x-ms-request-id 值，发出请求的大概时间，订阅、作业集合和/或作业的标识符，以及请求尝试进行的操作类型。

## <a name="see-also"></a>另请参阅
 [计划程序是什么？](scheduler-intro.md)

 [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)

 [开始在 Azure 门户中使用计划程序](scheduler-get-started-portal.md)

 [Azure 计划程序中的计划和计费](scheduler-plans-billing.md)

 [Azure 计划程序 REST API 参考](https://msdn.microsoft.com/library/mt629143)

 [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)

 [Azure 计划程序的高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 计划程序出站身份验证](scheduler-outbound-authentication.md)

