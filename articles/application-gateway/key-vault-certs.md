---
title: 使用密钥保管库证书的 SSL 终止
description: 了解如何通过集成 Azure 应用程序网关与密钥保管库的附加到启用 HTTPS 侦听器的服务器证书。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 37707d56caabf0ae8b0020eb8714245a27501ea6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696498"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>使用密钥保管库证书的 SSL 终止

[Azure 密钥保管库](../key-vault/key-vault-whatis.md)是可以使用来保护机密、 密钥和 SSL 证书的平台托管的密钥存储。 应用程序网关支持的附加到启用 HTTPS 侦听器的服务器证书 （在公共预览版） 与密钥保管库集成。 此支持仅限于应用程序网关 v2 SKU。

> [!IMPORTANT]
> 应用程序网关密钥保管库集成当前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

有两个模型用于 SSL 终止此公共预览版：

- 您可以显式提供附加到侦听器的 SSL 证书。 这是传递到应用程序网关的 SSL 证书，为 SSL 终端的传统模型。
- 您可以选择提供对现有密钥保管库证书的引用或机密期间 HTTPS 启用侦听器创建。

有很多密钥保管库集成的好处包括：

- 由于 SSL 证书未直接由应用程序开发团队的更强安全性。 与密钥保管库集成，项目单独的安全组可以预配，控制生命周期，并授予相应权限，以选择访问证书存储在密钥保管库中的应用程序网关。
- 支持现有的证书导入密钥保管库，或使用密钥保管库 Api 来创建和管理新的证书与任何受信任的密钥保管库合作伙伴。
- 存储在密钥保管库自动续订的证书的支持。

应用程序网关当前支持仅适用于软件验证证书。 不支持的硬件安全模块 (HSM) 验证证书。 一旦应用程序网关配置为使用密钥保管库证书，其实例从密钥保管库中检索证书，并为 SSL 终端本地安装它们。 实例也会定期轮询密钥保管库按 24 小时的时间间隔来检索已续订的版本的证书，如果它存在。 如果找到更新的证书，则会自动旋转当前与 HTTPS 侦听器相关联的 SSL 证书。

## <a name="how-it-works"></a>工作原理

与密钥保管库的集成需要三个步骤配置过程：

1. **创建用户分配的托管的标识**

   必须创建或重新使用现有用户分配托管的标识，应用程序网关使用代表用户检索密钥保管库证书。 有关详细信息，请参阅[什么是 Azure 资源的管理的标识？](../active-directory/managed-identities-azure-resources/overview.md) 此步骤在受用于创建标识的订阅的 Azure AD 租户中创建一个新的标识。
1. **配置密钥保管库**

   然后必须导入或通过应用程序网关运行的应用程序使用 Key Vault 中创建新的证书。 密钥保管库机密存储为无密码的基本 64 编码的 PFX 文件还可在此步骤。 使用证书类型是首选由于提供的密钥保管库中的证书类型对象的自动续订功能。 一旦创建证书或密钥时，必须在密钥保管库，以允许标识要向其授予定义访问策略*获取*提取机密的访问权限。

1. **配置应用程序网关**

   完成前两个步骤后，可以设置或修改现有的应用程序网关以使用用户分配的托管的标识。 你还可以配置 HTTP 侦听器的 SSL 证书，以指向完整 URI 的密钥保管库的证书或密钥 id。

![Key Vault 证书](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>后续步骤

[使用 Key Vault 证书使用 Azure PowerShell 配置 SSL 终止](configure-keyvault-ps.md)。