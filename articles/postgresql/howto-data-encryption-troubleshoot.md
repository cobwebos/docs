---
title: Azure Database for PostgreSQL 的数据加密-单服务器故障排除
description: 了解如何排查 Azure Database for PostgreSQL 单服务器的数据加密问题
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 6d2e027c47bef3186f95c2183b316b5c15511070
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371496"
---
# <a name="troubleshooting-data-encryption-with-customer-managed-keys-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-单服务器中的客户托管密钥的数据加密疑难解答
本文介绍如何识别并解决在使用客户托管密钥配置了数据加密的 Azure Database for PostgreSQL 单服务器上发生的常见问题/错误。

## <a name="introduction"></a>介绍
如果数据加密配置为在 Azure Key Vault 中使用客户托管的密钥，则服务器需要持续访问此密钥才能保持可用。 如果服务器在 Azure Key Vault 中失去了对客户管理的密钥的访问权限，则服务器将使用相应的错误消息拒绝所有连接，并将其状态更改为 "***无法访问***Azure 门户"。

如果无法访问 Azure Database for PostgreSQL-不需要单一服务器，可以立即将其删除以停止产生成本。 在对 Azure 密钥保管库的访问权限恢复并且服务器恢复可用之前，不允许服务器上的所有其他操作。 如果服务器是使用客户管理的进行加密的，则不能在无法访问的服务器上将数据加密选项从 "是" （客户管理）更改为 "否" （服务管理）。 您必须手动重新验证密钥，使服务器恢复可用。 这是保护数据免遭未经授权的访问的必要，而对客户托管的密钥的权限已被吊销。

## <a name="common-errors-causing-server-to-become-inaccessible"></a>导致服务器无法访问的常见错误

将数据加密与 Azure Key Vault 一起使用时所发生的大多数问题都是由以下错误配置之一引起的-

密钥保管库不可用或不存在

* 密钥保管库遭意外删除。
* 间歇性网络错误导致密钥保管库不可用。

无权访问密钥保管库或密钥不存在

* 密钥遭意外删除、禁用或密钥已到期。
* 意外删除了 Azure Database for PostgreSQL 单服务器实例管理的标识。
* 授予密钥的 Azure Database for PostgreSQL 托管标识的权限不够（它们不包括 Get、Wrap 和解包）。
* 已吊销或删除 Azure Database for PostgreSQL 单一服务器实例管理的标识的权限。

## <a name="identify-and-resolve-common-errors"></a>发现和解决常见错误
### <a name="errors-on-the-key-vault"></a>密钥保管库上的错误

#### <a name="disabled-key-vault"></a>禁用的密钥保管库
* AzureKeyVaultKeyDisabledMessage
* **说明**：无法在服务器上完成操作，因为已禁用 Azure Key Vault 项。

#### <a name="missing-key-vault-permissions"></a>缺少密钥保管库权限
* AzureKeyVaultMissingPermissionsMessage
* 服务器没有对 Azure Key Vault 权限所需的 Get、Wrap 和解包权限。 向 ID 为的服务主体授予任何缺少的权限。

### <a name="mitigation"></a>缓解操作
* 确认 Key Vault 中存在客户托管的密钥：
* 标识密钥保管库，然后在 Azure 门户中转到密钥保管库。
* 确保密钥 URI 标识的密钥存在。


## <a name="next-steps"></a>后续步骤
[使用 Azure 门户通过用于 PostgreSQL 的 Azure 数据库的客户托管密钥设置数据加密](howto-data-encryption-portal.md)。