---
title: Azure Key Vault 身份验证基础知识
description: 了解密钥保管库的身份验证模式的工作原理
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: c8d2304017a8fccd83c9c64559b8c5edf48481b7
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604271"
---
# <a name="key-vault-authentication-fundamentals"></a>Key Vault 身份验证基础知识

Azure Key Vault 允许你安全地存储和管理应用程序凭据（例如机密、密钥和证书），并将其存储在一个中心安全的云存储库中。 Key Vault 无需在应用程序中存储凭据。 应用程序可以在运行时进行身份验证以检索凭据 Key Vault。

作为管理员，你可以严格控制哪些用户和应用程序可以访问你的密钥保管库，并且可以限制和审核他们执行的操作。 本文档介绍密钥保管库访问模型的基本概念。 它将为您提供一个介绍性的知识，并向您展示如何从一开始就将用户或应用程序的身份验证到密钥保管库。

## <a name="required-knowledge"></a>必备知识

本文档假设你熟悉以下概念。 如果你不熟悉其中的任何概念，请在继续操作之前，请单击 "帮助" 链接。

* Azure Active Directory [链接](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
* 安全主体 [链接](https://docs.microsoft.com/azure/key-vault/general/authentication#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Key Vault 配置步骤摘要

1. 将 Azure Active Directory 中的用户或应用程序注册为安全主体。
1. 在 Azure Active Directory 中配置安全主体的角色分配。
1. 为安全主体配置密钥保管库访问策略。
1. 配置 Key Vault 防火墙对密钥保管库的访问， (可选) "。
1. 测试安全主体访问密钥保管库的能力。

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>将 Azure Active Directory 中的用户或应用程序注册为安全主体

当用户或应用程序向密钥保管库发出请求时，必须先通过 Azure Active Directory 对该请求进行身份验证。 为此，需要在 Azure Active Directory 中将用户或应用程序注册为安全主体。

请按照下面的文档链接来了解如何在 Azure Active Directory 中注册用户或应用程序。
**确保为用户注册创建密码，并为应用程序创建客户端密码或客户端证书凭据。**

* Azure Active Directory[链接](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)注册用户
* Azure Active Directory[链接](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)注册应用程序

## <a name="assign-your-security-principal-a-role-in-azure-active-directory"></a>在 Azure Active Directory 中为安全主体分配角色

Azure Active Directory 使用基于角色的访问控制 (RBAC) 向安全主体分配权限。 这些权限称为角色分配。

在密钥保管库的上下文中，这些角色分配决定了安全主体对管理平面的访问权限级别 (也称为密钥保管库) 的控制平面。 这些角色分配不会直接提供对数据平面机密的访问权限，但它们提供了管理密钥保管库的属性的访问权限。 例如，分配到 "读取者" **角色** 的用户或应用程序将不允许对 key vault 防火墙设置进行更改，而分配有 " **参与者" 角色** 的用户或应用程序可以进行更改。 对于机密、密钥和证书（例如创建或检索它们的值），这两个角色都不会直接访问它们，直到它们被分配到密钥保管库数据平面的访问权限。 下一步将对此进行介绍。

>[!IMPORTANT]
> 尽管具有参与者或所有者角色的用户无权在默认情况下对密钥保管库中存储的机密执行操作，但 "参与者" 和 "所有者" 角色提供了向存储在密钥保管库中的机密添加或删除访问策略的权限。 因此，具有这些角色分配的用户可以向自己授予访问密钥保管库中机密的权限。 出于此原因，建议只有管理员才有权访问参与者或所有者角色。 只需从密钥保管库检索机密的用户和应用程序应被授予 "读取者" 角色。 **下一部分中的更多详细信息。**

>[!NOTE]
> 在 Azure Active Directory 租户级别向用户分配角色分配时，此权限集将滴分配范围内的所有订阅、资源组和资源。 若要遵循最低权限的主体，你可以在更详细的范围内进行此角色分配。 例如，可以向用户分配订阅级别的 "读取者" 角色，并为单个密钥保管库分配所有者角色。 请访问 "标识访问管理" (订阅、资源组或密钥保管库的 IAM) 设置，以便在更精细的范围内分配角色。

* 若要详细了解 Azure Active Directory 角色 [链接](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
* 若要详细了解如何分配或删除角色分配 [链接](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>为安全主体配置密钥保管库访问策略

为用户和应用程序授予访问密钥保管库的访问权限之前，请务必了解可以在密钥保管库中执行的不同类型的操作。 Key vault 操作的主要类型有两种，管理平面 (也称为控制平面) 操作和数据平面操作。

此表显示了管理平面和数据平面控制的不同操作的几个示例。 更改 key vault 属性的操作是管理平面操作。 更改或检索密钥保管库中存储的机密值的操作是数据平面操作。

|管理平面操作 (示例) |数据平面操作 (示例) |
| --- | --- |
| 创建密钥保管库 | 创建密钥、机密和证书
| 删除 Key Vault | 删除密钥、机密和证书
| 添加或删除 Key Vault 角色分配 | 列出并获取密钥、机密和证书的值
| 添加或删除 Key Vault 访问策略 | 备份和还原密钥、机密和证书
| 修改 Key Vault 防火墙设置 | 续订密钥、机密和证书
| 修改 Key Vault 恢复设置 | 清除或恢复软删除的密钥、机密和证书
| 修改 Key Vault 诊断日志设置

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>管理平面访问 & Azure Active Directory 角色分配

Azure Active Directory 角色分配授予访问权限，以便对密钥保管库执行管理平面操作。 通常会将此访问权限授予用户，而不是应用程序。 可以通过更改用户的角色分配来限制用户可以执行的管理平面操作。

例如，向用户分配 Key Vault 读者角色的用户将允许他们查看密钥保管库的属性，例如访问策略，但不会允许用户进行任何更改。 如果分配用户，所有者角色将允许他们对更改密钥保管库管理平面设置具有完全访问权限。

角色分配在 key vault 访问控制 (IAM) 边栏选项卡中进行控制。 如果希望特定用户拥有访问权限，或者是多个密钥保管库资源的管理员，可以在保管库、资源组或订阅级别创建角色分配，并将角色分配添加到分配范围内的所有资源。

可以通过以下两种方式之一添加数据平面访问权限，或对密钥保管库中存储的密钥、机密和证书执行操作的访问权限。

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>数据平面访问选项1：经典 Key Vault 访问策略

密钥保管库访问策略向用户和应用程序授予对密钥保管库执行数据平面操作的访问权限。

> [!NOTE]
> 此访问模型与密钥保管库 RBAC 不兼容 (选项 2) 下面所述。 您必须选择一个。 单击密钥保管库的 "访问策略" 选项卡时，你将有机会做出此选择。

经典访问策略是精确的，这意味着你可以允许或拒绝每个用户或应用程序在 key vault 中执行单独操作的能力。 以下是一些示例：

* 安全主体1可以执行任何密钥操作，但不允许执行任何机密或证书操作。
* 安全主体2可以列出和读取所有密钥、机密和证书，但不能执行任何创建、删除或更新操作。
* 安全主体3可以备份和还原所有机密，但不能读取机密本身的值。

但是，经典访问策略不允许按对象级别的权限，并且分配的权限将应用于单个密钥保管库的作用域。 例如，如果向特定密钥保管库中的安全主体授予 "机密获取" 访问策略权限，则安全主体能够获取该特定密钥保管库中的所有机密。 但是，此 "获取机密" 权限不会自动扩展到其他密钥保管库，必须显式分配。

> [!IMPORTANT]
> 经典密钥保管库访问策略和 Azure Active Directory 角色分配相互独立。 如果在订阅级别分配 "参与者" 角色，则不会自动使安全主体能够对订阅范围内的每个密钥保管库执行数据平面操作。 必须仍必须授予安全主体，或授予自己执行数据平面操作的访问策略权限。

### <a name="data-plane-access-option-2--key-vault-rbac-preview"></a>数据平面访问选项2： Key Vault RBAC (预览版) 

向密钥保管库数据平面授予访问权限的一种新方式是通过密钥保管库基于角色的访问控制 (RBAC) 。

> [!NOTE]
> 此访问模型与上面显示的 key vault 经典访问策略不兼容。 您必须选择一个。 单击密钥保管库的 "访问策略" 选项卡时，你将有机会做出此选择。

Key Vault 角色分配是一组 Azure 内置角色分配，其中包含用于访问密钥、机密和证书的公用权限集。 此权限模型还启用了经典密钥保管库访问策略模型中未提供的其他功能。

* 通过允许用户在订阅、资源组或单独的密钥保管库级别分配这些角色，可以大规模管理 RBAC 权限。 用户将具有对 RBAC 赋值范围内的所有密钥保管库的数据平面权限。 这样就不必为每个用户/应用程序为每个密钥保管库分配单独的访问策略权限。

* RBAC 权限与 Privileged Identity Management 或 PIM 兼容。 这允许你为特权角色（如 Key Vault 管理员）配置实时访问控制。 这是一种最佳安全做法，并通过消除对密钥保管库的持续访问权限来遵循最低权限原则。

* RBAC 权限与每个对象的粒度权限兼容，因此，你可以限制用户仅对某些 key vault 对象执行操作。 这允许多个应用程序共享一个密钥保管库，同时仍然隔离应用程序之间的访问权限。

若要了解有关 Key Vault RBAC 的详细信息，请参阅以下文档：

* Azure Key Vault RBAC [链接](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac)
*  (预览版 Azure Key Vault RBAC 角色) [链接](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#key-vault-administrator-preview)

## <a name="configure-key-vault-firewall"></a>配置 Key Vault 防火墙

默认情况下，key vault 允许来自公共 internet 的流量通过公共终结点发送到密钥保管库。 为了增加安全性，你可以将 Azure Key Vault 防火墙配置为限制对密钥保管库公共终结点的访问。

若要启用 key vault 防火墙，请在 key vault 门户中单击 "网络" 选项卡，然后选择单选按钮，以允许从 "专用终结点和所选网络" 进行访问。 如果选择启用 key vault 防火墙，则可以通过 key vault 防火墙来允许流量。

* 将 IPv4 地址添加到 key vault 防火墙允许列表中。 此选项最适用于具有静态 IP 地址的应用程序。

* 将虚拟网络添加到密钥保管库防火墙。 此选项最适用于具有动态 IP 地址（例如虚拟机）的 Azure 资源。 可以将 Azure 资源添加到虚拟网络，并将虚拟网络添加到 key vault 防火墙允许列表中。 此选项使用作为虚拟网络中的专用 IP 地址的服务终结点。 这提供了一层额外的保护，因此密钥保管库与虚拟网络之间的流量不会通过公共 internet 进行路由。 若要详细了解服务终结点，请参阅以下文档。 [连接](https://docs.microsoft.com/azure/key-vault/general/network-security)

* 将专用链接连接添加到密钥保管库。 此选项会将你的虚拟网络直接连接到密钥保管库的特定实例，从而有效地将密钥保管库放在虚拟网络中。 若要了解有关配置到 key vault 的专用终结点连接的详细信息，请参阅以下 [链接](https://docs.microsoft.com/azure/key-vault/general/private-link-service)

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>测试服务主体访问密钥保管库的能力

完成上述所有步骤后，便可以设置和检索密钥保管库中的机密。

### <a name="authentication-process-for-users-examples"></a>用户的身份验证过程 (示例) 

* 用户可以登录到 Azure 门户以使用密钥保管库。 [Key Vault 门户快速入门](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal)

* 用户可以使用 Azure CLI 来使用密钥保管库。 [Key Vault Azure CLI 快速入门](https://docs.microsoft.com/azure/key-vault/general/quick-create-cli)

* 用户可以使用 Azure PowerShell 来使用密钥保管库。 [Key Vault Azure PowerShell 快速入门](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory 应用程序或服务的身份验证过程 (示例) 

* 应用程序在函数中提供客户端密码和客户端 ID，以获取 Azure Active Directory 标记。 

* 应用程序提供了用于获取 Azure Active Directory 令牌的证书。 

* Azure 资源使用 MSI 身份验证获取 Azure Active Directory 令牌。 

* 详细了解 MSI 身份验证 [链接](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

### <a name="authentication-process-for-application-python-example"></a>适用于应用程序 (Python 示例的身份验证过程) 

使用以下代码示例测试应用程序是否可以使用配置的服务主体从密钥保管库检索机密。

>[!NOTE]
>此示例仅用于演示和测试目的。 **不要在生产环境中使用客户端密钥身份验证** 这不是一种安全的设计做法。 作为最佳做法，应使用客户端证书或 MSI 身份验证。

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIEND SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>后续步骤

若要更详细地了解密钥保管库身份验证，请参阅以下文档。 [Key Vault 身份验证](https://docs.microsoft.com/azure/key-vault/general/authentication)
