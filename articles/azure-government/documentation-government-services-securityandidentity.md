---
title: "Azure 政府版安全性 + 标识 |Microsoft Docs"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: e2fe7983-5870-43e9-ae01-2d45d3102c8a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: cd01170c3c0a3f62024de3357d342af1f4f90c6c
ms.openlocfilehash: 27d447e8e3c336bbce2e1ca81d2c7c413b0360fc
ms.lasthandoff: 11/17/2016


---
# <a name="azure-government-security--identity"></a>Azure 政府版安全性 + 标识
## <a name="key-vault"></a>密钥保管库
有关此服务以及如何使用此服务的详细信息，请参阅 [Azure 密钥保管库公共文档](../key-vault/index.md)。

### <a name="data-considerations"></a>数据注意事项
以下信息标识 Azure 密钥保管库的 Azure Government 边界：

| 允许的管制/控制数据 | 不允许的管制/控制数据 |
| --- | --- |
| 使用 Azure 密钥保管库加密的所有数据都可以包含管制/控制数据。 |Azure 密钥保管库元数据不允许包含导出控制数据。 此元数据包括创建和维护密钥保管库时输入的所有配置数据。  请勿将管制/控制数据输入到以下字段：资源组名称、密钥保管库名称、订阅名称 |

密钥保管库已在 Azure Government 中正式发布。 作为正式发布的内容，其无任何扩展，因此密钥保管库仅可通过 PowerShell 和 CLI 获取。

## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府博客</a>。


