---
title: Azure Key Vault 版本
description: 各种版本的 Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 58c5fdcfef5e866d0c69d65412582c0ed649f7c6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230819"
---
# <a name="key-vault-versions"></a>Key Vault 版本

下面是 Azure Key Vault 的是。 新功能和改进也在[Azure 更新 Key Vault 频道](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)上公布。

## <a name="june-2020"></a>2020 年 6 月

Key Vault 的 Azure Monitor 现在为预览版。  Azure Monitor 提供 Key Vault 请求、性能、故障和延迟的统一视图，从而全面监视密钥保管库。 有关详细信息，请参阅[Key Vault (预览) Azure Monitor。](../../azure-monitor/insights/key-vault-insights-overview.md)

## <a name="may-2020"></a>2020 年 5 月

Key Vault "自带密钥" (BYOK) 现已正式发布。 请参阅[AZURE KEY VAULT BYOK 规范](../keys/byok-specification.md)，并了解如何将[受 HSM 保护的密钥导入到 Key Vault (BYOK) ](../keys/hsm-protected-keys-byok.md)。

## <a name="march-2020"></a>2020 年 3 月

专用终结点现已在预览版中提供。 Azure 专用链接服务使你能够通过虚拟网络中的专用终结点访问 Azure Key Vault 和 Azure 托管的客户/合作伙伴服务。  了解如何将[Key Vault 与 Azure Private Link 集成](private-link-service.md)。

## <a name="2019"></a>2019

- 发布下一代 Azure Key Vault Sdk。 有关其用法的示例，请参阅[Python](../secrets/quick-create-python.md)、 [.net](../secrets/quick-create-net.md)、 [Java](../secrets/quick-create-java.md)和[Node.js](../secrets/quick-create-node.md)的 Azure Key Vault 密钥快速入门
- 新的 Azure 策略用于管理密钥保管库证书。 请参阅[Key Vault 的 Azure 策略内置定义](../policy-samples.md)。
- Azure Key Vault 虚拟机扩展现已正式发布。  请参阅[Key Vault 适用于 Linux 的虚拟机扩展](../../virtual-machines/extensions/key-vault-linux.md)和[适用于 Windows 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-windows.md)。
- Azure 事件网格中现提供的 Azure Key Vault 事件驱动的机密管理。 有关详细信息，请参阅 [Azure Key Vault 中事件的事件网格架构] (。/../event-grid/event-schema-key-vault.md]，了解如何[通过 Azure 事件网格接收和响应 key vault 通知](event-grid-tutorial.md)。

## <a name="2018"></a>2018

本年度发布的新功能和集成：

- 与 Azure Functions 集成。 有关利用密钥保管库操作[Azure Functions](../../azure-functions/index.yml)的示例方案，请参阅[自动轮换密钥](../secrets/tutorial-rotation.md)。 
- [与 Azure Databricks 集成](/azure/databricks/scenarios/store-secrets-azure-key-vault)。 这样，Azure Databricks 现在支持两种类型的机密作用域： Azure Key Vault 支持和 Databricks。 有关详细信息，请参阅[创建 Azure Key Vault 支持的密钥作用域](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)
- [Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)。

## <a name="2016"></a>2016

本年度发布的新功能：

- 托管存储帐户密钥。 通过存储帐户密钥功能，可以更轻松地与 Azure 存储集成。 参阅概述主题[托管存储帐户密钥概述](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)，了解详细信息。
- 软删除。 软删除功能可改善密钥保管库和密钥保管库对象的数据保护。 参阅概述主题[软删除概述](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)，了解详细信息。

## <a name="2015"></a>2015

本年度发布的新功能：
- 证书管理。 作为功能添加到了2015-06-01 年9月 26 2016 日 GA 版本。

2015-06-01 年6月 24 2015 日公布 (版本) 。 此版本进行了以下更改： 
- 删除键-"使用" 字段已删除。
- 获取有关删除的键-"使用" 字段的信息。
- 将密钥导入到保管库中删除了 "使用" 字段。
- 还原密钥-"使用" 字段已删除。     
- 已将 RSA 算法的 "RSA_OAEP" 更改为 "RSA-OAEP"。 请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。    
 
第二个预览版本 (版本 2015-02-01-预览版) 于年4月 20 2015 日发布。 有关详细信息，请参阅 [REST API 更新](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) 博客文章。 更新了以下任务：
 
- 列出保管库中的密钥 - 向操作添加了分页支持。
- 列出密钥版本 - 添加了列出密钥版本的操作。  
- 列出保管库中的机密 - 添加了分页支持。
- 列出机密版本 - 添加了列出机密版本的操作。  
- 所有操作 - 添加了属性创建/更新时间戳。  
- 创建机密 - 向机密添加了 Content-Type。
- 创建密钥 - 添加了标记作为可选信息。
- 创建机密 - 添加了标记作为可选信息。
- 更新密钥 - 添加了标记作为可选信息。
- 更新机密 - 添加了标记作为可选信息。
- 将机密的最大大小从 10 KB 更改为 25 KB。 请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。    
 
## <a name="2014"></a>2014
 
第一个预览版 (版本 2014-12-08-预览版) 于2015年1月8日发布。  
 
## <a name="next-steps"></a>后续步骤

- [关于键、密钥和证书](about-keys-secrets-certificates.md)
- [“键”](../keys/index.yml)
- [机密](../secrets/index.yml)
- [证书](../certificates/index.yml)
