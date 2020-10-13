---
title: 使用客户管理的密钥加密配置数据
description: 使用客户管理的密钥加密配置数据
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 8942c93b7346613b8cfdc97d9afe09f1c473fb10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87384865"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>使用客户管理的密钥加密应用程序配置数据
Azure 应用程序配置[对敏感信息进行静态加密](../security/fundamentals/encryption-atrest.md)。 通过使用客户管理的密钥，可以管理加密密钥，从而提供增强的数据保护。  使用托管密钥加密时，将使用用户提供的 Azure Key Vault 密钥对应用程序配置中的所有敏感信息进行加密。  这样就可以根据需要轮换加密密钥。  还可以通过撤消应用程序配置实例对密钥的访问权限，来撤消 Azure 应用程序配置对敏感信息的访问权限。

## <a name="overview"></a>概述 
Azure 应用程序配置使用 Microsoft 提供的 256 位 AES 加密密钥来静态加密敏感信息。 每个应用程序配置实例都有其自己的加密密钥，该密钥由服务管理并用于加密敏感信息。 敏感信息包括在键值对中找到的值。  启用客户管理的密钥功能后，应用程序配置将使用分配给应用程序配置实例的托管标识对 Azure Active Directory 进行身份验证。 然后，托管标识调用 Azure Key Vault 并包装应用程序配置实例的加密密钥。 然后存储已包装的加密密钥，并在应用程序配置中将已解包的加密密钥缓存一小时。 应用程序配置每小时刷新应用程序配置实例加密密钥的已解包版本。 这可确保在正常操作情况下的可用性。 

>[!IMPORTANT]
> 如果分配给应用程序配置实例的标识不再有权解包实例的加密密钥，或者如果永久删除了托管密钥，则将无法再对存储在应用程序配置实例中的敏感信息进行解密。 使用 Azure Key Vault 的 [软删除](../key-vault/general/soft-delete-overview.md) 功能减少了意外删除加密密钥的几率。

当用户在其 Azure 应用程序配置实例上启用客户管理的密钥功能时，他们将控制服务访问其敏感信息的能力。 托管密钥用作根加密密钥。 用户可以通过更改其密钥保管库访问策略来撤消其应用程序配置实例对其托管密钥的访问权限。 撤消此访问权限时，应用程序配置将在一小时内失去解密用户数据的能力。 此时，应用程序配置实例将禁止所有访问尝试。 可通过再次向服务授予对托管密钥的访问权限来从这种情况中恢复。  在一小时内，应用程序配置将能够对用户数据进行解密，并在正常情况下运行。

>[!NOTE]
>所有 Azure 应用程序配置数据都可在独立备份中存储最多 24 小时。 这包括已解包的加密密钥。 此数据不能立即供服务或服务团队使用。 发生紧急还原时，Azure 应用程序配置将从托管的密钥数据中重新撤消。

## <a name="requirements"></a>要求
若要成功启用 Azure 应用程序配置的客户管理的密钥功能，需要以下组件：
- 标准层 Azure 应用程序配置实例
- 已启用软删除和清除保护功能的 Azure Key Vault
- Key Vault 中的 RSA 或 RSA-HSM 密钥
    - 此密钥不能过期，必须启用，并且必须启用包装和解包功能

配置这些资源后，再执行两个步骤即可允许 Azure 应用程序配置使用 Key Vault 密钥：
1. 将托管标识分配到 Azure 应用程序配置实例
2. 在目标 Key Vault 的访问策略中授予标识 `GET`、`WRAP` 和 `UNWRAP` 权限。

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>为 Azure 应用程序配置实例启用客户管理的密钥加密
若要开始，需要正确配置 Azure 应用程序配置实例。 如果还没有可用的应用程序配置实例，请按照以下快速入门进行设置：
- [使用 Azure 应用配置创建 ASP.NET Core 应用](quickstart-aspnet-core-app.md)
- [使用 Azure 应用程序配置创建 .NET Core 应用](quickstart-dotnet-core-app.md)
- [使用 Azure 应用配置创建 .NET Framework 应用](quickstart-dotnet-app.md)
- [使用 Azure 应用配置创建 Java Spring 应用](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的命令行指令。  它预安装了常用的 Azure 工具，其中包括 .NET Core SDK。 如果你已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。  若要详细了解 Azure Cloud Shell，请[阅读我们的文档](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>创建和配置 Azure Key Vault
1. 使用 Azure CLI 创建 Azure Key Vault。  请注意，`vault-name` 和 `resource-group-name` 均由用户提供，并且必须是唯一的。  在这些示例中，我们使用 `contoso-vault` 和 `contoso-resource-group`。

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. 为 Key Vault 启用软删除和清除保护。 替换在步骤 1 中创建的 Key Vault (`contoso-vault`) 和资源组 (`contoso-resource-group`) 的名称。

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. 创建 Key Vault 密钥。 为此密钥提供唯一的 `key-name`，并替换在步骤 1 中创建的 Key Vault (`contoso-vault`) 的名称。 指定首选 `RSA` 还是 `RSA-HSM` 加密。

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    此命令的输出显示生成的密钥的密钥 ID（“kid”）。  请记录密钥 ID 以便在本练习的后面使用。  密钥 ID 的格式为：`https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`。  密钥 ID 包含三个重要组件：
    1. Key Vault URI：`https://{my key vault}.vault.azure.net
    1. Key Vault 密钥名称：{Key Name}
    1. Key Vault 密钥版本：{Key version}

1. 使用 Azure CLI 创建系统分配的托管标识，替换前面步骤中使用的应用程序配置实例和资源组的名称。 托管标识将用于访问托管密钥。 我们使用 `contoso-app-config` 来说明应用程序配置实例的名称：
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    此命令的输出包含系统分配的标识的主体 ID（“principalId”）和租户 ID（“tenandId”）。  这些 ID 将用于向标识授予对托管密钥的访问权限。

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Azure 应用程序配置实例的托管标识需要访问密钥才能执行密钥验证、加密和解密。 它需要访问的特定操作集包括：密钥的 `GET`、`WRAP` 和 `UNWRAP`。  授予访问权限需要应用程序配置实例的托管标识的主体 ID。 此值是在上一步中获得的。 如下所示为 `contoso-principalId`。 使用命令行向托管密钥授予权限：

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Azure 应用程序配置实例可以访问托管密钥后，便可以使用 Azure CLI 在服务中启用客户管理的密钥功能。 请记住在密钥创建步骤的过程中记录的以下属性：`key name` `key vault URI`。

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

现在，Azure 应用程序配置实例已配置为使用存储在 Azure Key Vault 中的客户管理的密钥。

## <a name="next-steps"></a>后续步骤
本文介绍了如何配置 Azure 应用程序配置实例，令其使用客户管理的密钥进行加密。  了解如何[将服务与 Azure 托管标识集成](howto-integrate-azure-managed-service-identity.md)。
