---
title: 在 Azure Key Vault 中存储凭据 | Microsoft Docs
description: 了解如何在 Azure Key Vault 中存储所使用的数据存储的凭据，以便在运行时 Azure 数据工厂可以自动检索这些凭据。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jingwang
ms.openlocfilehash: 9a71a455ac4f406695edf722bc83604539eccaa9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="store-credential-in-azure-key-vault"></a>在 Azure Key Vault 中存储凭据

可以在 [Azure Key Vault](../key-vault/key-vault-whatis.md) 中存储数据存储和计算的凭据。 执行使用数据存储/计算的活动时，Azure 数据工厂将检索凭据。

目前，除自定义活动之外的所有活动类型都支持此功能。 具体而言，对于连接器配置，请查阅[每个连接器主题](copy-activity-overview.md#supported-data-stores-and-formats)中的“链接服务属性”部分了解详细信息。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-introduction.md)。

## <a name="prerequisites"></a>先决条件

此功能依赖于数据工厂服务标识。 从[数据工厂服务标识](data-factory-service-identity.md)了解其工作原理，并确保数据工厂已关联一个服务标识。

## <a name="steps"></a>Steps

若要引用 Azure Key Vault 中存储的凭据，需要：

1. 通过复制与工厂一起生成的“服务标识应用程序 ID”的值**[检索数据工厂服务标识](data-factory-service-identity.md#retrieve-service-identity)**。
2. **授予服务标识对 Azure Key Vault 的访问权限**。 在密钥保管库中 -> 访问策略 -> 新增 -> 搜索此服务标识应用程序 ID，以在“机密权限”下拉列表中授予“获取”权限。 它允许此指定的工厂访问密钥保管库中的机密。
3. **创建指向 Azure Key Vault 的链接服务**。 请参阅 [Azure Key Vault 链接服务](#azure-key-vault-linked-service)。
4. **创建数据存储链接服务，该服务中引用密钥保管库中存储的相应机密**。 请参阅[引用密钥保管库中存储的机密](#reference-secret-stored-in-key-vault)。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 链接服务

Azure Key Vault 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**AzureKeyVault**。 | 是 |
| baseUrl | 指定 Azure Key Vault URL。 | 是 |

**示例：**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>引用密钥保管库中存储的机密

在引用密钥保管库机密的链接服务中配置字段时，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 字段的 type 属性必须设置为：**AzureKeyVaultSecret**。 | 是 |
| secretName | Azure Key Vault 中机密的名称。 | 是 |
| secretVersion | Azure Key Vault 中机密的版本。<br/>如果未指定，将始终使用最新版本的机密。<br/>如果指定，然后它遵循给定的版本。| 否 |
| store | 指用于存储凭据的 Azure Key Vault 链接服务。 | 是 |

**示例：（请参阅“密码”部分）**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。