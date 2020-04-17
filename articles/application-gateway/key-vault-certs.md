---
title: 使用 Azure 密钥保管库证书终止 TLS
description: 了解如何将 Azure 应用程序网关与 Key Vault 集成，以便存储附加到支持 HTTPS 的侦听器的服务器证书。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: be558c3e3a68ce6c194dcf98d8f5ff92c4c14edb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457818"
---
# <a name="tls-termination-with-key-vault-certificates"></a>使用密钥保管库证书的 TLS 终止

[Azure 密钥保管库](../key-vault/general/overview.md)是一个平台管理的秘密存储，可用于保护机密、密钥和 TLS/SSL 证书。 Azure 应用程序网关支持与密钥保管库集成，以存储附加到支持 HTTPS 的侦听器的服务器证书。 此支持仅限 v2 SKU 版应用程序网关。

密钥保管库集成为 TLS 端接提供了两种模型：

- 您可以显式提供附加到侦听器的 TLS/SSL 证书。 此模型是将 TLS/SSL 证书传递到应用程序网关以进行 TLS 终止的传统方法。
- 可以选择在创建支持 HTTPS 的侦听器时提供对现有 Key Vault 证书或机密的引用。

应用程序网关与 Key Vault 集成具有许多优势，其中包括：

- 更强的安全性，因为 TLS/SSL 证书不是由应用程序开发团队直接处理的。 集成允许独立的安全团队执行以下操作：
  * 设置应用程序网关。
  * 控制应用程序网关生命周期。
  * 授权选定的应用程序网关访问存储在密钥保管库中的证书。
- 支持将现有证书导入密钥保管库中。 或者使用 Key Vault API 与任何受信任的 Key Vault 合作伙伴一起创建并管理新证书。
- 支持自动续订存储在密钥保管库中的证书。

应用程序网关目前仅支持经软件验证的证书。 不支持硬件安全模块 (HSM) 验证的证书。 将应用程序网关配置为使用密钥保管库证书后，其实例将从密钥保管库检索证书，并在本地安装以进行 TLS 终止。 实例还按 24 小时的时间间隔轮询 Key Vault，以便检索续订版的证书（如果存在）。 如果找到更新的证书，则当前与 HTTPS 侦听器关联的 TLS/SSL 证书将自动旋转。

> [!NOTE]
> Azure 门户仅支持密钥库证书，不支持机密。 应用程序网关仍然支持引用 KeyVault 的机密，但只能通过非门户资源（如 PowerShell、CLI、API、ARM 模板等）来引用机密。 

## <a name="how-integration-works"></a>集成工作原理

应用程序网关与 Key Vault 集成需要一个三步配置过程：

1. **创建用户分配的托管标识**

   你创建或重用现有的用户分配的托管标识，供应用程序网关用来代表你从 Key Vault 检索证书。 有关详细信息，请参阅[Azure 资源的托管标识是什么？](../active-directory/managed-identities-azure-resources/overview.md) 这一步在 Azure Active Directory 租户中创建新标识。 此标识受那个用来创建标识的订阅的信任。

1. **配置密钥保管库**

   然后导入现有的证书，或者在密钥保管库中创建新证书。 此证书将供通过应用程序网关的应用程序使用。 在此步骤中，也可使用密钥保管库机密，该机密作为无密码的 base 64 编码的 PFX 文件存储。 我们建议使用证书类型是因为适用于密钥保管库中证书类型对象的自动续订功能。 在创建证书或机密以后，即可在密钥保管库中定义访问策略，此类策略允许为标识授予对机密的“获取”** 访问权限。

1. **配置应用程序网关**

   在完成前面的两个步骤以后，即可设置或修改现有的应用程序网关，以便使用用户分配的托管标识。 您还可以配置 HTTP 侦听器的 TLS/SSL 证书，以指向密钥保管库证书或密钥 ID 的完整 URI。

   ![密钥保管库证书](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>后续步骤

[使用 Azure PowerShell 使用密钥保管库证书配置 TLS 终止](configure-keyvault-ps.md)
