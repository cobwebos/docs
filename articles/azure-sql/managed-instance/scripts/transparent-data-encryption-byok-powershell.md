---
title: PowerShell：启用“创建自己的密钥”(BYOK) TDE
titleSuffix: Azure SQL Managed Instance
description: 了解如何配置 Azure SQL 托管实例，以开始使用创建自己的密钥 (BYOK) 透明数据加密 (TDE) 通过 PowerShell 进行静态加密。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323208"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>使用 Azure Key Vault 中自己的密钥在 SQL 托管实例中实现透明数据加密

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

此 PowerShell 脚本实例使用 Azure Key Vault 中的密钥为 Azure SQL 托管实例配置使用客户托管密钥的透明数据加密 (TDE)。 这通常称为 TDE 的创建自己的密钥 (BYOK) 方案。 若要了解详细信息，请参阅[使用客户管理的密钥进行 Azure SQL 透明数据加密](../../database/transparent-data-encryption-byok-overview.md)。

## <a name="prerequisites"></a>先决条件

- 现有的托管实例。 请参阅[使用 PowerShell 创建托管实例](create-configure-managed-instance-powershell.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

在本地使用 PowerShell 或使用 Azure Cloud Shell 需要 Azure PowerShell 2.3.2 或更高版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)，或运行以下示例脚本，以便为当前用户安装该模块：

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="sample-scripts"></a>示例脚本 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

SQL 托管实例的其他 PowerShell 脚本示例可在[ Azure SQL 托管实例 PowerShell 脚本](../../database/powershell-script-content-guide.md)中找到。
