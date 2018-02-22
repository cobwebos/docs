---
title: "将 Key Vault 与 Azure 中 Windows VM 上的 SQL Server 集成（经典）| Microsoft 文档"
description: "了解如何自动配置用于 Azure 密钥保管库的 SQL Server 加密。 本主题说明了如何将 Azure 密钥保管库集成和经典部署模型中创建的 SQL Server 虚拟机结合使用。"
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fd0fb1f8ac9bb0132c64c195d4cc9c86ef8edd0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>在 Azure 虚拟机上配置 SQL Server 的 Azure Key Vault 集成（经典）
> [!div class="op_single_selector"]
> * [资源管理器](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [经典](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>概述
SQL Server 加密功能多种多样，包括[透明数据加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[列级加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) 和[备份加密](https://msdn.microsoft.com/library/dn449489.aspx)。 这些加密形式要求管理和存储用于加密的加密密钥。 Azure 密钥保管库 (AKV) 服务专用于在一个高度可用的安全位置改进这些密钥的安全性和管理。 [SQL Server 连接器](http://www.microsoft.com/download/details.aspx?id=45344)使 SQL Server 能够使用 Azure 密钥保管库中的这些密钥。

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[资源管理器和经典模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

如果在本地计算机上运行 SQL Server，请[按照此处步骤通过本地 SQL Server 计算机访问 Azure 密钥保管库](https://msdn.microsoft.com/library/dn198405.aspx)。 但对于 Azure VM 中的 SQL Server，可以使用 *Azure 密钥保管库集成*功能节省时间。 通过使用几个 Azure PowerShell cmdlet 来启用此功能，可以自动为 SQL VM 进行必要的配置以便访问密钥保管库。

启用此功能后，它会自动安装 SQL Server 连接器，配置 EKM 提供程序以访问 Azure Key Vault，并创建允许你访问保管库的凭据。 在前面提到的本地文档列出的步骤中，可以看到此功能自动完成步骤 2 和步骤 3。 仍需手动执行的唯一操作是创建密钥保管库和密钥。 之后，会自动进行 SQL VM 的整个设置。 在此功能完成设置后，可以执行 T-SQL 语句，以按照通常的方式加密数据库或备份。

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>配置 AKV 集成
使用 PowerShell 来配置 Azure 密钥保管库集成。 以下各节概述了所需的参数，并提供了一个示例 PowerShell 脚本。

### <a name="install-the-sql-server-iaas-extension"></a>安装 SQL Server IaaS 扩展
首先，请[安装 SQL Server IaaS 扩展](../classic/sql-server-agent-extension.md)。

### <a name="understand-the-input-parameters"></a>了解输入参数
下表列出在下一节中运行 PowerShell 脚本所需的参数。

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| **$akvURL** |**密钥保管库 URL** |“https://contosokeyvault.vault.azure.net/” |
| **$spName** |**服务主体名称** |“fde2b411-33d5-4e11-af04eb07b669ccf2” |
| **$spSecret** |**服务主体密码** |“9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =” |
| **$credName** |**凭据名称**：AKV 集成在 SQL Server 内创建一个凭据，使 VM 具有对密钥保管库的访问权限。 为此凭据选择一个名称。 |“mycred1” |
| **$vmName** |**虚拟机名称**：以前创建的 SQL VM 的名称。 |“myvmname” |
| **$serviceName** |**服务名称**：与 SQL VM 关联的云服务名称。 |“mycloudservicename” |

### <a name="enable-akv-integration-with-powershell"></a>使用 PowerShell 启用 AKV 集成
**New-AzureVMSqlServerKeyVaultCredentialConfig** cmdlet 为 Azure 密钥保管库集成功能创建配置对象。 **Set-AzureVMSqlServerExtension** 通过 **KeyVaultCredentialSettings** 参数配置此集成。 以下步骤显示如何使用这些命令。

1. 在 Azure PowerShell 中，首先使用特定的值配置输入参数，如本主题前面各节中所述。 下面的脚本就是一个示例。
   
        $akvURL = "https://contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. 然后使用以下脚本来配置和启用 AKV 集成。
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

SQL IaaS 代理扩展将使用此新配置来更新 SQL VM。

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

