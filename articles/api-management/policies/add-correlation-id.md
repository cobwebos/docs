---
title: Azure API 管理策略示例 - 添加包含相关 ID 的标头 | Microsoft Docs
description: Azure API 管理策略示例 - 演示如何将包含相关 ID 的标头添加到入站请求。
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
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 68f42124369194124ae1f8ebb93834a5be4e0128
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287365"
---
# <a name="add-a-header-containing-a-correlation-id"></a>添加包含相关 ID 的标头

本文介绍 Azure API 管理策略示例，该示例演示如何将包含相关 ID 的标头添加到入站请求。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中。

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)

