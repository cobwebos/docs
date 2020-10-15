---
title: API 管理策略示例-使用外部授权者授权请求
titleSuffix: Azure API Management
description: Azure API 管理策略示例 - 展示了如何使用封装自定义或旧身份验证/授权逻辑的外部授权程序授权请求。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076226"
---
# <a name="authorize-requests-using-external-authorizer"></a>使用外部授权程序授权请求

本文中的 Azure API 管理策略示例展示了如何使用封装自定义身份验证/授权逻辑的外部授权程序来保护 API 访问。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-reference.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中  。

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [访问限制策略](../api-management-access-restriction-policies.md)
+ [策略示例](../policy-reference.md)