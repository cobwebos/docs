---
title: 示例 API 管理策略 - 向后端服务发送请求上下文信息
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 演示如何将请求上下文信息发送到后端服务。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 8cef989ca7ce8ee649c4f4aeb579c43bf8a8d6ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442403"
---
# <a name="send-request-context-information-to-the-backend-service"></a>将请求上下文信息转发到后端服务

本文介绍 Azure API 管理策略示例，该示例演示如何将请求上下文信息发送到后端服务。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中****。

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)

