---
title: "Azure API 管理策略示例 - 生成共享访问签名 | Microsoft Docs"
description: "Azure API 管理策略示例 - 演示如何使用表达式生成共享访问签名并使用 rewrite-uri 策略将请求转发到 Azure 存储。"
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
ms.openlocfilehash: 9b0d37e4f7930389d3399e51de905db2b2ce8c27
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="generate-shared-access-signature"></a>生成共享访问签名

本文介绍 Azure API 管理策略示例，该示例演示如何使用表达式生成[共享访问签名](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1)并使用 rewrite-uri 策略将请求转发到 Azure 存储。 若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

## <a name="policy"></a>策略

将代码粘贴到“入站”块中。

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)

