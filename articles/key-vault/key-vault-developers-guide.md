---
title: Azure 密钥保管库开发人员指南
description: 开发人员可以使用 Azure 密钥保管库来管理 Microsoft Azure 环境中的加密密钥。
services: key-vault
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 10/12/2017
ms.author: alleonar
ms.openlocfilehash: 7ff8c038ac5fa42668227a0531fa77bd853dd2b2
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757513"
---
# <a name="azure-key-vault-developers-guide"></a>Azure 密钥保管库开发人员指南

使用 Key Vault 可以从应用程序中安全地访问敏感信息：

- 无需自己编写代码即可保护密钥和机密信息，并且能够轻松地在应用程序中使用它们。
- 能够让客户拥有和管理其自己的密钥，因此可以专注于提供核心软件功能。 这样，应用程序便不会对客户的租户密钥和机密承担职责或潜在责任。
- 应用程序可以使用密钥进行签名和加密，不过使密钥管理与应用程序分开，可以使解决方案适用于地理分散的应用。
- 自 2016 年 9 月版本的 Key Vault 发布起，应用程序现在可以使用 Key Vault [证书](https://docs.microsoft.com/rest/api/keyvault/certificate-operations)。 有关详细信息，请参阅[关于密钥、机密和证书](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates)。

有关 Azure 密钥保管库的更多常规信息，请参阅[什么是密钥保管库](key-vault-whatis.md)。

## <a name="public-previews"></a>公共预览版

我们会定期发布新 Key Vault 功能的公共预览版。 抢先试用这些版本，并通过反馈电子邮件地址 azurekeyvault@microsoft.com 将想法告诉我们。

### <a name="storage-account-keys---july-10-2017"></a>存储帐户密钥 - 2017 年 7 月 10 日

>[!NOTE]
>在 Azure Key Vault 的此更新版中，只有**存储帐户密钥**功能以预览版提供。

此预览版包含通过 [.NET/C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/)、[REST](https://docs.microsoft.com/rest/api/keyvault/) 和 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 接口提供的新存储帐户密钥功能。 

有关新存储帐户密钥功能的详细信息，请参阅 [Azure Key Vault 存储帐户密钥概述](key-vault-ovw-storage-keys.md)。

## <a name="videos"></a>视频

此视频介绍如何创建自己的密钥保管库以及如何使用从“Hello Key Vault”示例应用程序使用它。

- [Key Vault 开发人员 - 快速入门指南](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

上述视频中提到的资源：

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure 密钥保管库代码示例](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>创建和管理密钥保管库

虽然 Azure Key Vault 可用于安全存储凭据以及其他密钥和机密，但代码需要通过 Key Vault 的身份验证才能检索它们。 托管服务标识 (MSI) 为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的自动托管标识，更巧妙地解决了这个问题。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。 

有关 MSI 的详细信息，请参阅 [Azure 资源的托管服务标识 (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview)。

若要详细了解如何使用 AAD，请参阅[将应用程序与 Azure Active Directory 集成](/azure/active-directory/develop/active-directory-integrating-applications)。

使用密钥保管库中的密钥、机密或证书前，请通过 CLI、PowerShell、资源管理器模板或 REST 创建和管理密钥保管库，如以下文章所述：

- [使用 CLI 创建和管理 Key Vault](key-vault-manage-with-cli2.md)
- [使用 PowerShell 创建和管理 Key Vault](key-vault-get-started.md)
- [通过 Azure 资源管理器模板创建密钥保管库并添加机密](../azure-resource-manager/resource-manager-template-keyvault.md)
- [使用 REST 创建和管理 Key Vault](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>使用密钥保管库进行编码

面向程序员的 Key Vault 管理系统包含多个接口。 此部分收录了所有语言和一些代码示例的链接。 

### <a name="supported-programming-and-scripting-languages"></a>支持的编程和脚本语言

#### <a name="rest"></a>REST

通过 REST 接口，可以访问所有 Key Vault 资源：保管库、密钥、机密等。 

[Key Vault REST API 参考](https://docs.microsoft.com/rest/api/keyvault/)。 

#### <a name="net"></a>.NET

[适用于 Key Vault 的 .NET API 参考](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

有关 .NET SDK 2.x 版的详细信息，请参阅[发行说明](key-vault-dotnet2api-release-notes.md)。

#### <a name="java"></a>Java

[适用于 Key Vault 的 Java SDK](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

在 Node.js 中，Key Vault 管理 API 和 Key Vault 对象 API 相互独立。 下面的概述文章介绍了如何访问这两个 API。 

[用于 Node.js 的 Azure Key Vault 模块](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[用于 Python 的 Azure Key Vault 库](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[适用于 Key Vault 的 Azure CLI](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[适用于 Key Vault 的 Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>快速入门指南

- [创建密钥保管库](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Node.js 中的 Key Vault 入门](https://azure.microsoft.com/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>代码示例

有关将密钥保管库用于应用程序的完整示例，请参阅：

- [Azure Key Vault 代码示例](http://www.microsoft.com/download/details.aspx?id=45343) - .NET 示例应用程序 *HelloKeyVault* 和 Azure Web 服务示例。 
- [从 Web 应用程序使用 Azure Key Vault](key-vault-use-from-web-application.md) - 此教程有助于你了解如何从 Azure 中的 Web 应用程序使用 Azure Key Vault。 

## <a name="how-tos"></a>操作方法

以下文章和方案提供了特定于任务的指导，以便使用 Azure Key Vault：

- [订阅移动后更改密钥保管库租户 ID](key-vault-subscription-move-fix.md) - 将 Azure 订阅从租户 A 移到租户 B 时，租户 B.中的主体（用户和应用程序）无法访问现有的密钥保管库。使用本指南解决此问题。
- [访问位于防火墙后面的密钥保管库](key-vault-access-behind-firewall.md) -若要访问密钥保管库，密钥保管库客户端应用程序必须能够访问各种功能的多个终结点。
- [如何生成和传输 Azure 密钥保管库的受 HSM 保护的密钥](key-vault-hsm-protected-keys.md) -这会帮助你计划、生成和传输自己的用于 Azure 密钥保管库的受 HSM 保护密钥。
- [如何在部署期间传递安全值（如密码）](../azure-resource-manager/resource-manager-keyvault-parameter.md) - 当需要在部署期间以参数形式传递安全值（例如密码）时，可以将该值存储为 Azure 密钥保管库中的机密，并在其他资源管理模板中引用该值。
- [如何使用密钥保管库，以便通过 SQL Server 进行可扩展的密钥管理](https://msdn.microsoft.com/library/dn198405.aspx) - 适用于 Azure 密钥保管库的 SQL Server 连接器允许 SQL Server 和 VM 中的 SQL 将 Azure 密钥保管库服务用作可扩展密钥管理 (EKM) 提供程序，以便保护其针对应用程序链接的加密密钥；透明数据加密、备份加密和列级加密。
- [如何将证书从密钥保管库部署到虚拟机](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - 在 Azure 虚拟机上运行的云应用程序需要证书。 现在，要如何将此证书部署到此 VM 中呢？
- [如何使用端到端密钥轮替和审核设置 Key Vault](key-vault-key-rotation-log-monitoring.md) - 逐步介绍如何设置 Azure Key Vault 的密钥轮替和审核。
- [通过 Key Vault 部署 Azure Web 应用证书]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)提供有关部署作为[应用服务证书](https://azure.microsoft.com/blog/internals-of-app-service-certificate/)产品的一部分存储在 Key Vault 中的证书的分步说明。
- [向多个应用程序授予 Key Vault 的访问权限](key-vault-group-permissions-for-apps.md) Key Vault 访问控制策略仅支持 16 个条目。 但是，可以创建一个 Azure Active Directory 安全组。 将所有关联的服务主体添加到此安全组，并为此安全组授予密钥保管库的访问权限。
- 如需更多将密钥保管库与 Azure 集成和结合使用的特定于任务的指导，请参阅[针对 Key Vault 的 Ryan Jones Azure 资源管理器模板示例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。
- [如何将 Key Vault 软删除与 CLI 配合使用](key-vault-soft-delete-cli.md)介绍了 Key Vault 的使用和生命周期以及各种已启用软删除的 Key Vault 对象。
- [如何将 Key Vault 软删除与 PowerShell 配合使用](key-vault-soft-delete-powershell.md)介绍了 Key Vault 的使用和生命周期以及各种已启用软删除的 Key Vault 对象。

## <a name="integrated-with-key-vault"></a>与密钥保管库集成

这些文章介绍了使用 Key Vault 或与之集成的其他方案和服务。

- [Azure 磁盘加密](../security/azure-security-disk-encryption.md)利用 Windows 的行业标准 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，为 OS 和数据磁盘提供卷加密。 该解决方案与 Azure 密钥保管库集成，可帮助你控制和管理密钥保管库订阅中的磁盘加密密钥和机密，同时确保虚拟机磁盘中的所有数据可在 Azure 存储空间中静态加密。
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) 提供了对帐户中存储的数据进行加密的选项。 对于密钥管理，Data Lake Store 提供两种用于管理主加密密钥 (MEK) 的模式，这两种模式可用于解密在 Data Lake Store 中存储的任何数据。 可以让 Data Lake Store 代为管理 MEK，或选择使用 Azure 密钥保管库帐户保留 MEK 所有权。 创建 Data Lake Store 帐户时可以指定密钥管理模式。 
- [Azure 信息保护](/information-protection/plan-design/plan-implement-tenant-key)允许管理自己的租户密钥。 例如，不是由 Microsoft 管理租户密钥（默认设置），可以管理自己的租户密钥，以遵守适用于组织的具体规定。 管理自己的租户密钥也称为自带密钥（简称 BYOK）。

## <a name="key-vault-overviews-and-concepts"></a>Key Vault 概述和概念

- [Key Vault 软删除行为](key-vault-ovw-soft-delete.md)介绍了一种可以恢复已删除的对象的功能（不管是有意还是无意删除）。
- [Key Vault 客户端限制](key-vault-ovw-throttling.md)介绍了有关限制的基本概念，并针对应用提供了限制方法。
- [Key Vault 存储帐户密钥概述](key-vault-ovw-storage-keys.md)介绍了 Key Vault 与 Azure 存储帐户密钥的集成。
- [Key Vault 安全体系](key-vault-ovw-security-worlds.md)介绍了区域与安全领域之间的关系。

## <a name="social"></a>社交

- [密钥保管库博客](http://aka.ms/kvblog)
- [密钥保管库论坛](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>支持库

- [Microsoft Azure Key Vault 核心库](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)提供 IKey 和 IKeyResolver 接口，用于通过标识符查找密钥，以及使用密钥执行操作。
- [Microsoft Azure 密钥保管库扩展](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions)为 Azure 密钥保管库提供扩展功能。


