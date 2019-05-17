---
title: 使用 Azure 密钥保管库证书的 SSL 终止
description: 了解如何通过集成 Azure 应用程序网关与密钥保管库的附加到已启用 HTTPS 的侦听器的服务器证书。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827628"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>使用密钥保管库证书的 SSL 终止

[Azure 密钥保管库](../key-vault/key-vault-whatis.md)是平台托管机密存储可用于保护机密、 密钥和 SSL 证书。 Azure 应用程序网关支持的附加到已启用 HTTPS 的侦听器的服务器证书 （在公共预览版） 与密钥保管库集成。 此支持仅限于应用程序网关 v2 SKU。

> [!IMPORTANT]
> 应用程序网关与密钥保管库集成当前处于公共预览状态。 此预览版不附带服务级别协议 (SLA) 提供，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此公共预览版提供 SSL 终端的两个模型：

- 您可以显式提供附加到侦听器的 SSL 证书。 此模型是要传递给应用程序网关的 SSL 证书，为 SSL 终端的传统方法。
- 当您创建已启用 HTTPS 的侦听器时，可以选择提供对现有密钥保管库证书或密钥的引用。

与密钥保管库的应用程序网关集成提供了许多好处，包括：

- 更强的安全性，因为 SSL 证书无法直接处理的应用程序开发团队。 集成允许到一个单独的安全团队：
  * 设置应用程序网关。
  * 控制应用程序网关生命周期。
  * 授予对所选应用程序网关的权限来访问存储在密钥保管库中的证书。
- 用于向密钥保管库中导入现有证书的支持。 或使用密钥保管库 Api 来创建和管理新的证书与任何受信任的密钥保管库合作伙伴。
- 支持的 key vault 中存储的证书自动续订。

应用程序网关当前支持软件验证证书。 硬件安全模块 (HSM)-不支持已验证的证书。 应用程序网关配置为使用密钥保管库证书后，其实例从密钥保管库中检索证书，并为 SSL 终端本地安装。 实例还会轮询密钥保管库在 24 小时的时间间隔来检索已续订的版本的证书，如果它存在。 如果找到更新的证书，则会自动旋转当前与 HTTPS 侦听器相关联的 SSL 证书。

## <a name="how-integration-works"></a>集成的工作原理

与密钥保管库的应用程序网关集成需要三个步骤配置过程：

1. **创建用户分配托管的标识**

   创建或重新使用现有用户分配托管的标识，应用程序网关用于代表用户检索密钥保管库证书。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。 此步骤在 Azure Active Directory 租户中创建一个新的标识。 用于创建标识的订阅信任的标识。

1. **配置密钥保管库**

   您然后导入现有证书或创建一个新的 key vault 中。 通过应用程序网关运行的应用程序将使用证书。 在此步骤中，您还可以使用存储为无密码、 基 64 编码的 PFX 文件的密钥保管库密钥。 我们建议使用证书类型，由于可使用密钥保管库中的证书类型对象的自动续订功能。 若要允许标识要向其授予密钥保管库中创建的证书或密钥后，定义访问策略*获取*密钥访问权限。

1. **配置应用程序网关**

   完成前面的两个步骤后，您可以设置或修改现有的应用程序网关使用用户分配托管的标识。 你还可以配置 HTTP 侦听器的 SSL 证书，以指向完整的 URI 的密钥保管库证书或密钥的 id。

   ![密钥保管库证书](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>后续步骤

[使用 Azure PowerShell 使用 Key Vault 证书配置 SSL 终止](configure-keyvault-ps.md)
