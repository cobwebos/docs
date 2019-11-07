---
title: 'PowerShell： Enable BYOK TDE-Azure SQL 数据库托管实例 '
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
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691407"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>使用 Azure Key Vault 中自己的密钥管理托管实例中的透明数据加密

此 PowerShell 脚本示例使用 Azure Key Vault 中的密钥，为 Azure SQL 托管实例的客户托管密钥配置透明数据加密（TDE）。 这通常称为 TDE 的创建自己的密钥方案。 若要了解有关 TDE 的客户托管密钥的详细信息，请参阅[TDE 创建自己的密钥到 AZURE SQL](../transparent-data-encryption-byok-azure-sql.md)。

## <a name="prerequisites"></a>先决条件

- 现有托管实例。 请参阅[使用 PowerShell 创建 Azure SQL 数据库托管实例](sql-database-create-configure-managed-instance-powershell.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

在本地使用 PowerShell 或使用 Azure Cloud Shell 需要 AZ PowerShell 2.3.2 或更高版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)，或运行下面的示例脚本，为当前用户安装模块：

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="sample-scripts"></a>示例脚本

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure SQL 数据库 PowerShell 脚本](../sql-database-powershell-samples.md)中找到更多 SQL 数据库 PowerShell 脚本示例。
