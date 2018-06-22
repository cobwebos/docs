---
title: Azure API 管理策略示例 - 使用外部授权程序授权请求 | Microsoft Docs
description: Azure API 管理策略示例 - 展示了如何使用封装自定义或旧身份验证/授权逻辑的外部授权程序授权请求。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 7d172a40f2aad65b595026fc656634060a1f7193
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284866"
---
# <a name="authorize-requests-using-external-authorizer"></a>使用外部授权程序授权请求

本文中的 Azure API 管理策略示例展示了如何使用封装自定义身份验证/授权逻辑的外部授权程序来保护 API 访问。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中。

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [访问限制策略](../api-management-access-restriction-policies.md)
+ [策略示例](../policy-samples.md)