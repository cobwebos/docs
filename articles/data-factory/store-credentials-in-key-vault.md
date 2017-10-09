---
title: "在 Azure Key Vault 中存储凭据 | Microsoft Docs"
description: "了解如何在 Azure Key Vault 中存储所使用的数据存储的凭据，以便在运行时 Azure 数据工厂可以自动检索这些凭据。"
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc8b4f1ecf664c7db5ab2e535245dee90415fa65
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="store-credential-in-azure-key-vault"></a>在 Azure Key Vault 中存储凭据

可以在 [Azure Key Vault](../key-vault/key-vault-whatis.md) 中存储数据存储的凭据。 执行使用数据存储的活动时，Azure 数据工厂将检索凭据。

> [!NOTE]
> 目前，仅 [Dynamics 连接器](connector-dynamics-crm-office-365.md)支持此功能。 

## <a name="steps"></a>步骤

创建数据工厂时，将在创建工厂的同时创建服务标识。 服务标识是注册到 Azure Activity Directory 的托管应用程序，表示此特定数据工厂。 可以通过 Azure 门户 -> 数据工厂->“属性”找到服务标识信息： 

- 服务标识 ID
- 服务标识租户
- 服务标识应用程序 ID

若要引用 Azure Key Vault 中存储的凭据，需要：

1. 复制随数据工厂一起生成的“服务标识应用程序 ID”。
2. 授予服务标识对 Azure Key Vault 的访问权限。 通过密钥保管库 ->“访问控制”->“添加”-> 搜索此服务标识应用程序 ID 添加读者权限。 它允许此指定的工厂访问密钥保管库中的机密。
3. 创建指向 Azure Key Vault 的链接服务。 请参阅 [Azure Key Vault 链接服务](#azure-key-vault-linked-service)。
4. 创建数据存储链接服务，该服务中引用密钥保管库中存储的相应机密。 请参阅[引用密钥保管库中存储的凭据](#reference-credential-stored-in-key-vault)。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 链接服务

Azure Key Vault 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**AzureKeyVault**。 | 是 |
| baseUrl | 指定 Azure Key Vault URL（DNS 名称）。 | 是 |

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

## <a name="reference-credential-stored-in-key-vault"></a>引用密钥保管库中存储的凭据

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
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
