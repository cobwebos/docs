---
title: SSL 终止，Azure Key Vault 证书
description: 了解如何将 Azure 应用程序网关与附加到支持 HTTPS 的侦听器的服务器证书 Key Vault 集成。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 5633dd7b72f4de22cd34b7d093e8ec4d9cb411f1
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137705"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>SSL 终止，Key Vault 证书

[Azure Key Vault](../key-vault/key-vault-overview.md)是平台管理的密钥存储，可用于保护机密、密钥和 SSL 证书。 对于附加到启用 HTTPS 的侦听器的服务器证书，Azure 应用程序网关支持与 Key Vault 集成。 此支持仅限于应用程序网关的 v2 SKU。

Key Vault 集成为 SSL 终止提供两种模型：

- 可以显式提供附加到侦听器的 SSL 证书。 此模型是将 SSL 证书传递给应用程序网关以进行 SSL 终止的传统方法。
- 创建启用 HTTPS 的侦听器时，可以选择提供对现有 Key Vault 证书或机密的引用。

应用程序网关与 Key Vault 的集成提供了许多好处，包括：

- 安全性更强，因为应用程序开发团队不会直接处理 SSL 证书。 集成允许单独的安全团队：
  * 设置应用程序网关。
  * 控制应用程序网关生命周期。
  * 向所选应用程序网关授予访问密钥保管库中存储的证书的权限。
- 支持将现有证书导入到密钥保管库。 或者使用 Key Vault Api 通过任何受信任的 Key Vault 合作伙伴创建和管理新证书。
- 支持自动续订密钥保管库中存储的证书。

应用程序网关当前仅支持软件验证的证书。 不支持硬件安全模块（HSM）验证的证书。 在应用程序网关配置为使用 Key Vault 证书后，其实例会从 Key Vault 检索证书，并在本地将其安装以进行 SSL 终止。 实例还会轮询时间间隔为24小时的 Key Vault，以检索证书的续订版本（如果存在）。 如果找到更新的证书，则将自动轮换当前与 HTTPS 侦听器关联的 SSL 证书。

> [!NOTE]
> Azure 门户仅支持 KeyVault 证书，而不支持机密。 应用程序网关仍支持从 KeyVault 引用机密，而只能通过 PowerShell、CLI、API、ARM 模板等非门户资源进行引用。 

## <a name="how-integration-works"></a>集成的工作方式

应用程序网关与 Key Vault 的集成需要三步配置过程：

1. **创建用户分配的托管标识**

   你可以创建或重复使用现有的用户分配的托管标识，应用程序网关使用该标识以你的名义 Key Vault 来检索证书。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。 此步骤会在 Azure Active Directory 租户中创建新的标识。 标识受用于创建标识的订阅信任。

1. **配置密钥保管库**

   然后导入现有证书，或在 key vault 中创建新证书。 通过应用程序网关运行的应用程序将使用该证书。 在此步骤中，还可以使用存储为不带密码的基本64编码的 PFX 文件的密钥保管库机密。 由于密钥保管库中的证书类型对象可用的自动续订功能，我们建议使用证书类型。 创建证书或机密后，可以在密钥保管库中定义访问策略，以允许授权标识*获得*对机密的访问权限。

1. **配置应用程序网关**

   完成上述两个步骤后，可以设置或修改现有的应用程序网关，以使用用户分配的托管标识。 你还可以配置 HTTP 侦听器的 SSL 证书，使其指向 Key Vault 证书或机密 ID 的完整 URI。

   ![Key vault 证书](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>后续步骤

[使用 Azure PowerShell 配置使用 Key Vault 证书的 SSL 终止](configure-keyvault-ps.md)
