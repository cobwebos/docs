---
title: Azure 密钥保管库开发人员指南
description: 开发人员可以使用 Azure 密钥保管库来管理 Microsoft Azure 环境中的加密密钥。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: a04435b1e2feb537231bb80d2777b9ea2599c241
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812397"
---
# <a name="azure-key-vault-developers-guide"></a>Azure 密钥保管库开发人员指南

使用 Key Vault 可以从应用程序中安全地访问敏感信息：

- 保护密钥、机密和证书，无需自行编写代码，并且可以轻松地从应用程序中使用它们。
- 您可以让客户拥有和管理其自己的密钥、机密和证书，以便您可以专注于提供核心软件功能。 这样，你的应用程序将不承担客户租户密钥、机密和证书的责任或潜在责任。
- 应用程序可以使用密钥进行签名和加密，但会使密钥管理在应用程序外部保持不变。 有关密钥的详细信息，请参阅 [关于密钥](../keys/about-keys.md)
- 你可以管理密码、访问密钥、sas 令牌等凭据，将它们存储在 Key Vault 作为机密，请参阅 [关于机密](../secrets/about-secrets.md)
- 管理证书。 有关详细信息，请参阅 [关于证书](../certificates/about-certificates.md)

有关 Azure Key Vault 的更多常规信息，请参阅[什么是 Key Vault](overview.md)。

## <a name="public-previews"></a>公共预览版

我们会定期发布新 Key Vault 功能的公共预览版。 试用公共预览版功能，并通过 azurekeyvault@microsoft.com 反馈电子邮件地址告诉我们你的想法。

## <a name="creating-and-managing-key-vaults"></a>创建和管理密钥保管库

与其他 Azure 服务一样，Key Vault 管理通过 Azure 资源管理器服务完成。 Azure 资源管理器是 Azure 的部署和管理服务。 它提供了一个管理层，用于在 Azure 帐户中创建、更新和删除资源。 有关详细信息，请参阅 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

对管理层的访问由 [Azure 基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/overview)来控制。 在 Key Vault 中，管理层也称为管理或控制平面，使你可以创建和管理密钥保管库及其属性（包括访问策略，但不包括密钥、机密和证书），这些策略是在数据平面上管理的。 你可以使用预定义的 `Key Vault Contributor` 角色来授予对 Key Vault 的管理访问权限。     

**用于密钥保管库管理的 API 和 Sdk：**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault)<br>[快速入门](quick-create-cli.md)|[引用](/powershell/module/az.keyvault)<br>[快速入门](quick-create-powershell.md)|[引用](/rest/api/keyvault/)|[引用](/azure/templates/microsoft.keyvault/vaults)|[引用](/dotnet/api/microsoft.azure.management.keyvault)|[引用](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[引用](/java/api/com.microsoft.azure.management.keyvault)|[引用](/javascript/api/@azure/arm-keyvault)|

请参阅安装包和源代码的 [客户端库](client-libraries.md) 。

有关 Key Vault 管理平面的详细信息，请参阅 [Key Vault 管理平面](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>在代码中对 Key Vault 进行身份验证

Key Vault 正在使用要求 Azure AD 安全主体授予访问权限 Azure AD 身份验证。 Azure AD 安全主体可以是用户、应用程序服务主体、 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)或任何类型的安全主体的组。

### <a name="authentication-best-practices"></a>身份验证最佳实践
建议将托管标识用于部署到 Azure 的应用程序。 如果你使用的是不支持托管标识的 Azure 服务，或者如果本地部署了应用程序，则可以 [使用具有证书的服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 。 在这种情况下，证书应存储在 Key Vault 并经常旋转。 使用机密的服务主体可用于开发和测试环境，并可在本地或使用用户主体 Cloud Shell。

Azure 标识客户端库支持上述身份验证方案，并与 Key Vault Sdk 集成。 可以跨不同的环境和平台使用 Azure 标识库，而无需更改代码。 Azure 标识还会自动从已登录到 Azure 用户的身份验证令牌中 Azure CLI、Visual Studio、Visual Studio Code 等。 

有关详细信息，请参阅： 

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)|[Azure 标识 SDK Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme)|[Azure 标识 SDK JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)|     

在应用程序中 Key Vault 进行身份验证：
- [在 .NET 中的虚拟机中托管的应用程序中 Key Vault 进行身份验证](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-virtual-machine)
- [在 Python 中托管的应用程序中 Key Vault 进行身份验证](https://docs.microsoft.com/azure/key-vault/general/tutorial-python-virtual-machine)
- [通过应用服务 Key Vault 进行身份验证](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-create-vault-azure-web-app)

## <a name="manage-keys-certificates-and-secrets"></a>管理密钥、证书和机密

访问密钥、机密和证书由数据平面控制。 可以使用本地保管库访问策略或 RBAC (预览) 来完成数据平面访问控制。

**密钥 Api 和 Sdk**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault/key)<br>[快速入门](../keys/quick-create-cli.md)|[引用](/powershell/module/az.keyvault/)<br>[快速入门](../keys/quick-create-powershell.md)|[引用](/rest/api/keyvault/#key-operations)|不适用|[引用](/dotnet/api/azure.security.keyvault.keys)|[引用](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[快速入门](../keys/quick-create-python.md)|[引用](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)|[引用](/javascript/api/@azure/keyvault-keys/)|

**证书 Api 和 Sdk**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault/certificate)<br>[快速入门](../certificates/quick-create-cli.md)|[引用](/powershell/module/az.keyvault)<br>[快速入门](../certificates/quick-create-powershell.md)|[引用](/rest/api/keyvault/#certificate-operations)|不适用|[引用](/dotnet/api/azure.security.keyvault.certificates)|[引用](/python/api/overview/azure/keyvault-certificates-readme)<br>[快速入门](../certificates/quick-create-python.md)|[引用](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)|[引用](/javascript/api/@azure/keyvault-certificates/)|

**机密 Api 和 Sdk**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[引用](/cli/azure/keyvault/secret)<br>[快速入门](../secrets/quick-create-cli.md)|[引用](/powershell/module/az.keyvault/)<br>[快速入门](../secrets/quick-create-powershell.md)|[引用](/rest/api/keyvault/#secret-operations)|[引用](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[快速入门](../secrets/quick-create-template.md)|[引用](/dotnet/api/azure.security.keyvault.secrets)<br>[快速入门](../secrets/quick-create-net.md)|[引用](/python/api/overview/azure/keyvault-secrets-readme)<br>[快速入门](../secrets/quick-create-python.md)|[引用](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[快速入门](../secrets/quick-create-java.md)|[引用](/javascript/api/@azure/keyvault-secrets/)<br>[快速入门](../secrets/quick-create-node.md)|

请参阅安装包和源代码的 [客户端库](client-libraries.md) 。

有关 Key Vault 数据平面安全性的详细信息，请参阅 [Key Vault 数据平面和访问策略](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) 和 [Key Vault 数据平面和 RBAC (预览) ](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-azure-rbac-preview)

### <a name="code-examples"></a>代码示例

有关在应用程序中使用密钥保管库的完整示例，请参阅：

- [Azure Key Vault 代码示例](https://azure.microsoft.com/resources/samples/?service=key-vault) - Azure Key Vault 的代码示例。 

## <a name="how-tos"></a>操作方法

以下文章和方案提供了特定于任务的指导，方便用户使用 Azure Key Vault：

- [访问防火墙后面的密钥保管库](access-behind-firewall.md) - 若要访问密钥保管库，密钥保管库客户端应用程序需要能够访问多个终结点才能使用各种功能。
- 如何将证书部署到 Key Vault- [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)、 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) （在 Azure 上的 VM 中运行的云应用程序）中的 vm 需要证书。 现在，如何将此证书部署到此 VM 中？
- [通过 Key Vault 部署 Azure Web 应用证书](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
- 分配访问策略（[CLI](assign-access-policy-cli.md) | [PowerShell](assign-access-policy-powershell.md) | [门户](assign-access-policy-portal.md)）。 
- [如何将 Key Vault 软删除与 CLI 配合使用](soft-delete-cli.md)介绍了 Key Vault 的使用和生命周期以及各种已启用软删除的 Key Vault 对象。
- [如何在部署期间传递安全值（如密码）](../../azure-resource-manager/templates/key-vault-parameter.md) - 需要在部署期间以参数形式传递安全值（例如密码）时，可以将该值存储为 Azure Key Vault 中的机密，并在其他资源管理模板中引用该值。

## <a name="integrated-with-key-vault"></a>与密钥保管库集成

这些文章介绍了使用 Key Vault 或与之集成的其他方案和服务。

- [静态加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) 允许在保留数据时对数据进行 (加密) 编码。 数据加密密钥通常由 Azure Key Vault 中的密钥加密密钥进行加密，以进一步限制访问。
- [Azure 信息保护](/azure/information-protection/plan-implement-tenant-key)允许管理自己的租户密钥。 例如，不是由 Microsoft 管理租户密钥（默认设置），可以管理自己的租户密钥，以遵守适用于组织的具体规定。 管理自己的租户密钥也称为自带密钥（简称 BYOK）。
- 使用[Azure 专用链接服务](private-link-service.md)，可以通过虚拟网络中的专用终结点访问 azure 服务 (例如，Azure Key Vault、azure 存储和 Azure Cosmos DB) 和 azure 托管的客户/合作伙伴服务。
- Key Vault 与 [事件网格](https://docs.microsoft.com/azure/event-grid/event-schema-key-vault)  的集成，用户可以在密钥保管库中存储的机密状态发生更改时收到通知。 你可以向应用程序分发新版本的机密，或轮换接近到期机密以防止中断。
- 你可以通过 Key Vault 中的不需要的访问来保护 [Azure Devops](https://docs.microsoft.com/azure/devops/pipelines/release/azure-key-vault) 机密。
- [使用 DataBricks 中存储 Key Vault 的机密连接到 Azure 存储](https://docs.microsoft.com/azure/key-vault/general/integrate-databricks-blob-storage)
- 在 Kubernetes 上配置并运行 [机密存储 CSI 驱动程序](https://docs.microsoft.com/azure/key-vault/general/key-vault-integrate-kubernetes) 的 Azure Key Vault 提供程序

## <a name="key-vault-overviews-and-concepts"></a>Key Vault 概述和概念

- [Key Vault 软删除行为](soft-delete-overview.md)介绍了一种可以恢复已删除的对象的功能（不管是有意还是无意删除）。
- [Key Vault 客户端限制](overview-throttling.md)介绍了有关限制的基本概念，并针对应用提供了限制方法。
- [Key Vault 安全体系](overview-security-worlds.md)介绍了区域与安全领域之间的关系。

## <a name="social"></a>社交

- [密钥保管库博客](https://aka.ms/kvblog)
- [密钥保管库论坛](https://aka.ms/kvforum)
