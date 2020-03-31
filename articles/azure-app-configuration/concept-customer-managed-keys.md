---
title: 使用客户管理的密钥加密配置数据
description: 使用客户管理的密钥加密配置数据
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 5749b2fc58c4e1c5c75142f85a5132946714e25b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472630"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>使用客户管理的密钥加密应用配置数据
Azure 应用程序配置[在静态时加密敏感信息](../security/fundamentals/encryption-atrest.md)。 使用客户管理的密钥允许您管理加密密钥，从而增强数据保护。  使用托管密钥加密时，应用配置中的所有敏感信息都使用用户提供的 Azure 密钥保管库密钥进行加密。  这提供了按需旋转加密密钥的能力。  它还提供了通过撤消应用配置实例对密钥的访问来撤消 Azure 应用配置对敏感信息的访问的能力。

## <a name="overview"></a>概述 
Azure 应用配置使用 Microsoft 提供的 256 位 AES 加密密钥对静态敏感信息进行加密。 每个应用配置实例都有自己的加密密钥，由服务管理，并用于加密敏感信息。 敏感信息包括键值对中找到的值。  启用客户托管密钥功能后，应用配置将使用分配给应用配置实例的托管标识对 Azure 活动目录进行身份验证。 然后，托管标识调用 Azure 密钥保管库并包装应用配置实例的加密密钥。 然后存储包装的加密密钥，并在应用配置中缓存未包装的加密密钥一小时。 应用配置每小时刷新应用配置实例加密密钥的未包装版本。 这可确保在正常操作条件下的可用性。 

>[!IMPORTANT]
> 如果分配给应用配置实例的标识不再授权解包实例的加密密钥，或者如果托管密钥被永久删除，则将不再能够解密存储在 App 中的敏感信息配置实例。 使用 Azure 密钥保管库的[软删除](../key-vault/key-vault-ovw-soft-delete.md)功能可减少意外删除加密密钥的可能性。

当用户在其 Azure 应用配置实例上启用客户托管密钥功能时，他们控制服务访问其敏感信息的能力。 托管密钥用作根加密密钥。 用户可以通过更改其密钥保管库访问策略来撤消其应用配置实例对其托管密钥的访问权限。 撤销此访问权限后，应用配置将失去在一小时内解密用户数据的能力。 此时，应用配置实例将禁止所有访问尝试。 此情况可以通过再次授予对托管密钥的服务访问权限来恢复。  在一小时内，应用配置将能够解密用户数据并在正常条件下运行。

>[!NOTE]
>所有 Azure 应用配置数据在隔离备份中存储长达 24 小时。 这包括未包装的加密密钥。 此数据不能立即提供给服务或服务团队。 在发生紧急还原时，Azure 应用配置将从托管密钥数据中重新撤消自身。

## <a name="requirements"></a>要求
成功启用 Azure 应用配置的客户管理密钥功能需要以下组件：
- 标准层 Azure 应用配置实例
- 启用了软删除和清除保护功能的 Azure 密钥保管库
- 密钥保管库中的 RSA 或 RSA-HSM 密钥
    - 密钥不能过期，必须启用，并且必须同时启用换行和解包功能

配置这些资源后，将保留两个步骤，以允许 Azure 应用配置使用密钥保管库密钥：
1. 将托管标识分配给 Azure 应用配置实例
2. 在目标密钥`GET`保管`WRAP`库的访问`UNWRAP`策略中授予标识 和 权限。

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>为 Azure 应用配置实例启用客户管理的密钥加密
首先，您需要一个正确配置的 Azure 应用配置实例。 如果尚未提供应用配置实例，请按照以下快速入门之一设置一个实例：
- [使用 Azure 应用配置创建 ASP.NET Core 应用](quickstart-aspnet-core-app.md)
- [使用 Azure 应用配置创建 .NET 核心应用](quickstart-dotnet-core-app.md)
- [使用 Azure 应用配置创建 .NET Framework 应用](quickstart-dotnet-app.md)
- [使用 Azure 应用配置创建 Java Spring 应用](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的命令行指令。  它预安装了常用的 Azure 工具，其中包括 .NET Core SDK。 如果你已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。  若要详细了解 Azure Cloud Shell，请[阅读我们的文档](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>创建和配置 Azure 密钥保管库
1. 使用 Azure CLI 创建 Azure 密钥保管库。  请注意，这`vault-name`两`resource-group-name`个 和 都是用户提供的，并且必须是唯一的。  我们使用这些`contoso-vault`示例`contoso-resource-group`并在这些示例中使用。

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. 为密钥保管库启用软删除和清除保护。 替换步骤 1 中创建的密钥`contoso-vault`保管库 （`contoso-resource-group`） 和资源组 （ ） 的名称。

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. 创建密钥保管库密钥。 为此键提供`key-name`唯一，并替换步骤 1 中创建的密钥保管`contoso-vault`库 （ ） 的名称。 指定是首选`RSA`还是`RSA-HSM`加密。

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    此命令的输出显示生成的密钥的密钥 ID（"儿童"）。  记下本练习稍后将使用的密钥 ID。  密钥 ID 的窗体为`https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`： 。  密钥 ID 有三个重要组件：
    1. 密钥保管库 URI：'https：//[我的密钥保管库].vault.azure.net
    1. 密钥保管库密钥名称： [密钥名称]
    1. 密钥保管库密钥版本：[密钥版本]

1. 使用 Azure CLI 创建分配的托管标识的系统，替换应用配置实例和前面步骤中使用的资源组的名称。 托管标识将用于访问托管密钥。 我们用于`contoso-app-config`说明应用配置实例的名称：
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    此命令的输出包括系统分配标识的主体 ID（"主体 Id"）和租户 ID（"tenandId"）。  这将用于授予对托管密钥的标识访问权限。

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Azure 应用配置实例的托管标识需要访问密钥才能执行密钥验证、加密和解密。 它需要访问的特定操作集包括：`GET`和`WRAP``UNWRAP`键。  授予访问权限需要应用配置实例的托管标识的主 ID。 此值在上一步中获得。 如下所示为`contoso-principalId`。 使用命令行向托管密钥授予权限：

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Azure 应用配置实例可以访问托管密钥后，我们可以使用 Azure CLI 在服务中启用客户管理的密钥功能。 回想在密钥创建步骤中记录的以下属性： `key name` `key vault URI`.

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Azure 应用配置实例现在配置为使用存储在 Azure 密钥保管库中的客户管理密钥。

## <a name="next-steps"></a>后续步骤
在本文中，您将 Azure 应用配置实例配置为使用客户管理的密钥进行加密。  了解如何[将服务与 Azure 托管标识集成](howto-integrate-azure-managed-service-identity.md)。