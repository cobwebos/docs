---
title: 使用 Azure Key Vault 证书实现 SSL 终止
description: 了解如何将 Azure 应用程序网关与 Key Vault 集成，以便存储附加到支持 HTTPS 的侦听器的服务器证书。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 725a9d67e6a6412fc48a4278b5a8a163272e5133
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000986"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>使用 Key Vault 证书实现 SSL 终止

[Azure Key Vault](../key-vault/key-vault-overview.md) 是由平台管理的机密存储，可以用来保证机密、密钥和 SSL 证书的安全。 Azure 应用程序网关支持与 Key Vault（公共预览版）集成，以存储附加到支持 HTTPS 的侦听器的服务器证书。 此支持仅限 v2 SKU 版应用程序网关。

> [!IMPORTANT]
> 将应用程序网关与 Key Vault 集成的功能目前为公共预览版。 此预览版在提供时没有附带服务级别协议 (SLA)，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

此公共预览版提供两种用于 SSL 终止的模型：

- 可以显式提供附加到侦听器的 SSL 证书。 此模型是将 SSL 证书传递到应用程序网关进行 SSL 终止的传统方式。
- 可以选择在创建支持 HTTPS 的侦听器时提供对现有 Key Vault 证书或机密的引用。

应用程序网关与 Key Vault 集成具有许多优势，其中包括：

- 更高的安全性，因为 SSL 证书不直接由应用程序开发团队处理。 集成允许独立的安全团队执行以下操作：
  * 设置应用程序网关。
  * 控制应用程序网关生命周期。
  * 授权选定的应用程序网关访问存储在密钥保管库中的证书。
- 支持将现有证书导入密钥保管库中。 或者使用 Key Vault API 与任何受信任的 Key Vault 合作伙伴一起创建并管理新证书。
- 支持自动续订存储在密钥保管库中的证书。

应用程序网关目前仅支持经软件验证的证书。 不支持硬件安全模块（HSM）验证的证书。 应用程序网关在配置为使用 Key Vault 证书以后，其实例会从 Key Vault 检索证书并将其安装到本地进行 SSL 终止。 实例还按 24 小时的时间间隔轮询 Key Vault，以便检索续订版的证书（如果存在）。 如果发现更新的证书，则目前与 HTTPS 侦听器关联的 SSL 证书会自动轮换。

## <a name="how-integration-works"></a>集成工作原理

应用程序网关与 Key Vault 集成需要一个三步配置过程：

1. **创建用户分配的托管标识**

   你创建或重用现有的用户分配的托管标识，供应用程序网关用来代表你从 Key Vault 检索证书。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。 这一步在 Azure Active Directory 租户中创建新标识。 此标识受那个用来创建标识的订阅的信任。

1. **配置密钥保管库**

   然后导入现有的证书，或者在密钥保管库中创建新证书。 此证书将供通过应用程序网关的应用程序使用。 在此步骤中，也可使用密钥保管库机密，该机密作为无密码的 base 64 编码的 PFX 文件存储。 我们建议使用证书类型是因为适用于密钥保管库中证书类型对象的自动续订功能。 在创建证书或机密以后，即可在密钥保管库中定义访问策略，此类策略允许为标识授予对机密的“获取”访问权限。

1. **配置应用程序网关**

   在完成前面的两个步骤以后，即可设置或修改现有的应用程序网关，以便使用用户分配的托管标识。 也可配置 HTTP 侦听器的 SSL 证书，使之指向 Key Vault 证书或机密 ID 的完整 URI。

   ![密钥保管库证书](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>后续步骤

[使用 Azure PowerShell 通过 KeyVault 证书配置 SSL 终止](configure-keyvault-ps.md)
