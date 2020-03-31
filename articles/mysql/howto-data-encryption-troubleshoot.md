---
title: 排除数据加密故障 - MySQL 的 Azure 数据库
description: 了解如何在 MySQL 的 Azure 数据库中排除数据加密故障
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 42956d115590fd322d2851fd546c505a76a851fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297034"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-mysql"></a>为 MySQL 在 Azure 数据库中排除数据加密故障

本文介绍如何使用客户管理的密钥配置数据加密时，如何识别和解决 MySQL Azure 数据库中可能出现的常见问题。

## <a name="introduction"></a>介绍

将数据加密配置为在 Azure 密钥保管库中使用客户管理的密钥时，服务器需要持续访问密钥。 如果服务器无法访问 Azure 密钥保管库中的客户托管密钥，它将拒绝所有连接、返回相应的错误消息，并将其状态更改为 Azure 门户中的***不可访问***状态。

如果不再需要 MySQL 服务器无法访问的 Azure 数据库，则可以将其删除以停止产生成本。 在还原密钥保管库且服务器可用之前，不允许对服务器上的其他操作。 当数据加密使用客户管理的密钥加密时，也不可能将数据`Yes`加密选项从（客户管理`No`）更改为不可访问的服务器上（服务管理）。 在再次访问服务器之前，您必须手动重新验证密钥。 此操作对于在吊销客户管理密钥的权限时防止数据被未经授权的访问是必需的。

## <a name="common-errors-that-cause-the-server-to-become-inaccessible"></a>导致服务器无法访问的常见错误

以下错误配置导致使用 Azure 密钥保管库密钥的数据加密问题：

- 密钥保管库不可用或不存在：
  - 密钥保管库遭意外删除。
  - 间歇性网络错误导致密钥保管库不可用。

- 您没有访问密钥保管库的权限，或者密钥不存在：
  - 密钥已过期或意外删除或禁用。
  - MySQL 实例的 Azure 数据库的托管标识被意外删除。
  - MySQL 实例的 Azure 数据库的托管标识具有足够的密钥权限。 例如，权限不包括获取、换行和取消包装。
  - MySQL 实例的 Azure 数据库的托管标识权限已吊销或删除。

## <a name="identify-and-resolve-common-errors"></a>发现和解决常见错误

### <a name="errors-on-the-key-vault"></a>密钥保管库上的错误

#### <a name="disabled-key-vault"></a>禁用密钥保管库

- `AzureKeyVaultKeyDisabledMessage`
- **说明**： 由于 Azure 密钥保管库密钥已禁用，因此无法在服务器上完成该操作。

#### <a name="missing-key-vault-permissions"></a>缺少密钥保管库权限

- `AzureKeyVaultMissingPermissionsMessage`
- **说明**：服务器没有 Azure 密钥保管库所需的获取、换行和取消包装权限。 使用 ID 向服务主体授予任何缺少的权限。

### <a name="mitigation"></a>缓解操作

- 确认密钥保管库中存在客户管理的密钥。
- 标识密钥保管库，然后在 Azure 门户中转到密钥保管库。
- 确保密钥 URI 标识存在密钥。

## <a name="next-steps"></a>后续步骤

[使用 Azure 门户在 MySQL 的 Azure 数据库上使用客户管理的密钥设置数据加密](howto-data-encryption-portal.md)
