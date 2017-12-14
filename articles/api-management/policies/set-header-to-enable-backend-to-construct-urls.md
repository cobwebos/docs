---
title: "Azure API 管理策略示例 - 添加 Forwarded 标头 | Microsoft Docs"
description: "Azure API 管理策略示例 - 演示如何在入站请求中添加 Forwarded 标头，以允许后端 API 构造正确的 URL。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: cc2df914532b6cda37c951b65b243e90b63d57cb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="add-a-forwarded-header"></a>添加 Forwarded 标头

本文介绍 Azure API 管理策略示例，该示例演示如何在入站请求中添加 Forwarded 标头，以允许后端 API 构造正确的 URL。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="code"></a>代码

将代码粘贴到“入站”块中。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)
