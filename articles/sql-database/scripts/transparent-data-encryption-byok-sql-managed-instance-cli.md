---
title: CLI 示例 - 启用 BYOK TDE - Azure SQL 数据库托管实例
description: 了解如何配置 Azure SQL 托管实例，以开始使用 BYOK 透明数据加密 (TDE) 通过 PowerShell 进行静态加密。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061737"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>使用 Azure Key Vault 中自己的密钥管理托管实例中的透明数据加密

此 Azure CLI 脚本示例使用 Azure 密钥保管库的密钥配置 Azure SQL 托管实例的客户托管密钥的透明数据加密 （TDE）。 这通常称为 TDE 的自带密钥方案。 若要详细了解使用客户托管密钥的 TDE，请参阅[适用于 Azure SQL 的支持“创建自己的密钥”的 TDE](../transparent-data-encryption-byok-azure-sql.md)。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

### <a name="prerequisites"></a>先决条件

现有的托管实例，请参阅[使用 Azure CLI 创建 Azure SQL 数据库托管实例](sql-database-create-configure-managed-instance-cli.md)。

### <a name="sign-in-to-azure"></a>登录 Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>运行脚本

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其关联的所有资源。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>样本参考

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | 数据库命令。 |
| [az sql 故障转移组](/cli/azure/sql/failover-group) | 故障转移组命令。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅[Azure CLI 文档](/cli/azure)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../sql-database-cli-samples.md)中找到。
