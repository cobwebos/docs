---
title: Azure API 管理策略示例 - 使用 OAuth2 在网关和后端之间进行授权 | Microsoft Docs
description: Azure API 管理策略示例 - 演示如何使用 OAuth2 在网关和后端之间进行授权。 该示例演示如何从 AAD 获取访问令牌并将其转发到后端。
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: fc896656a4725475fc78cadb5bab54a27cfc02a2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>使用 OAuth2 在网关和后端之间进行授权

本文介绍 Azure API 管理策略示例，该示例演示如何使用 OAuth2 在网关和后端之间进行授权。 该示例演示如何从 AAD 获取访问令牌并将其转发到后端。 

若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

以下脚本使用了 {{property}} 中出现的属性。 若要了解各个属性以及如何在 API 管理策略中使用它们，请参阅[此](../api-management-howto-properties.md)主题。
 
## <a name="policy"></a>策略

将代码粘贴到“入站”块中。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)

