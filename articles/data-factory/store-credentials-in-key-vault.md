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
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: a7d440509e2b823400cde83c1ac2ec054c37eb74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311873"
---
# <a name="store-credential-in-azure-key-vault"></a>在 Azure Key Vault 中存储凭据

可以在 [Azure Key Vault](../key-vault/key-vault-whatis.md) 中存储数据存储和计算的凭据。 执行使用数据存储/计算的活动时，Azure 数据工厂将检索凭据。

目前，除自定义活动之外的所有活动类型都支持此功能。 具体而言，对于连接器配置，请查阅[每个连接器主题](copy-activity-overview.md#supported-data-stores-and-formats)中的“链接服务属性”部分了解详细信息。

## <a name="prerequisites"></a>必备组件

此功能依赖于数据工厂托管标识。 了解如何从工作[托管数据工厂的标识](data-factory-service-identity.md)，并确保你的数据工厂已关联的一个。

## <a name="steps"></a>Steps

若要引用 Azure Key Vault 中存储的凭据，需要：

1. **检索数据工厂托管标识**通过复制"服务标识应用程序 ID"与工厂一起生成的值。 如果使用 ADF 创作用户界面，将在 Azure Key Vault 链接的服务创建窗口; 显示托管的标识应用程序 ID此外可以检索从 Azure 门户中，请参阅[检索数据工厂托管标识](data-factory-service-identity.md#retrieve-managed-identity)。
2. **授予 Azure 密钥保管库托管的标识的访问权限。** 在密钥保管库中-> 的访问策略-> 新增-> 的搜索此管理标识应用程序 ID，授予**获取**机密权限下拉列表中的权限。 它允许此指定的工厂访问密钥保管库中的机密。
3. **创建指向 Azure Key Vault 的链接服务**。 请参阅 [Azure Key Vault 链接服务](#azure-key-vault-linked-service)。
4. **创建数据存储链接服务，该服务中引用密钥保管库中存储的相应机密**。 请参阅[引用密钥保管库中存储的机密](#reference-secret-stored-in-key-vault)。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 链接服务

Azure Key Vault 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**AzureKeyVault**。 | 是 |
| baseUrl | 指定 Azure Key Vault URL。 | 是 |

**使用创作 UI：**

单击“连接” -> “链接服务” -> “+新建”，然后搜索“Azure Key Vault”：

![搜索 AKV](media/store-credentials-in-key-vault/search-akv.png)

选择凭证所在的已预配的 Azure Key Vault。 可执行“测试连接”操作，确保 AKV 连接有效。 

![配置 AKV](media/store-credentials-in-key-vault/configure-akv.png)

**JSON 示例：**

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

**使用创作 UI：**

创建与数据存储/计算的连接时，为机密字段选择“Azure Key Vault”。 选择已预配的 Azure Key Vault 链接服务并提供机密名称。 也可根据需要提供机密版本。 

>[!TIP]
>对于连接器 （如 SQL Server) Blob 存储，等等，链接服务中使用的连接字符串可以选择要存储在 AKV 中中的机密字段如密码，或将整个连接字符串存储在 AKV 中。 您可以找到这两个选项在 UI 上。

![配置 AKV 机密](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON 示例：（请参阅“密码”部分）**

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
