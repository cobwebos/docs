---
title: PowerShell：启用 BYOK TDE - Azure SQL 数据库托管实例 | Microsoft Docs
description: 了解如何配置 Azure SQL 托管实例，以开始使用 BYOK 透明数据加密 (TDE) 通过 PowerShell 进行静态加密。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 04/19/2019
ms.openlocfilehash: d8f0f4a8e603a9040d166b00682077cff23abd8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569704"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault-preview"></a>使用 Azure Key Vault (预览版) 中的密钥管理托管实例中的透明数据加密

此 PowerShell 脚本示例使用 Azure Key Vault 中的密钥, 为 Azure SQL 托管实例的创建自己的密钥 (预览) 方案中的透明数据加密 (TDE) 配置。 若要详细了解支持“创建自己的密钥”(BYOK) 的 TDE，请参阅[适用于 Azure SQL 的支持“创建自己的密钥”的 TDE](../transparent-data-encryption-byok-azure-sql.md)。

## <a name="prerequisites"></a>系统必备

- 现有托管实例。 请参阅[使用 PowerShell 创建 AZURE SQL 数据库托管实例](sql-database-create-configure-managed-instance-powershell.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本地或使用 Azure Cloud Shell 都需要 AZ PowerShell 1.1.1-预览版或更高版本的预览版本。 如果需要升级, 请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps), 或运行下面的示例脚本以安装模块。

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="sample-scripts"></a>示例脚本

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。
