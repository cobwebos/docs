---
title: "与在 Azure （经典） 中的 Windows Vm 上的 SQL Server 集成的密钥保管库 |Microsoft 文档"
description: "了解如何自动执行的与 Azure 密钥保管库一起使用的 SQL Server 加密配置。 本主题说明如何在经典部署模型中创建虚拟机的 SQL 服务器上使用 Azure 密钥保管库集成。"
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
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
ms.openlocfilehash: 2a9ac5763bb934bd0646e47c3936f7bdd0d603b1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Azure 虚拟机 （经典） 上配置 SQL Server 的 Azure 密钥保管库集成
> [!div class="op_single_selector"]
> * [资源管理器](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [经典](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>概述
有多个 SQL Server 加密功能，如[透明数据加密 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)，[列级加密 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)，和[备份加密](https://msdn.microsoft.com/library/dn449489.aspx)。 这些加密形式要求你以管理和存储用于加密的加密密钥。 Azure 密钥保管库 (AKV) 服务旨在提高的安全性和管理这些密钥在一个安全且高度可用的位置。 [SQL Server 连接器](http://www.microsoft.com/download/details.aspx?id=45344)使 SQL Server 以使用 Azure 密钥保管库中的这些密钥。

> [!IMPORTANT] 
> Azure 具有用于创建和使用资源的两个不同的部署模型：[资源管理器和经典](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

SQL Server 正在使用本地计算机，如果有[你可以遵循步骤来从本地 SQL Server 计算机访问 Azure 密钥保管库](https://msdn.microsoft.com/library/dn198405.aspx)。 但对于在 Azure Vm 中的 SQL Server，你可以通过使用节省时间*Azure 密钥保管库集成*功能。 通过几个 Azure PowerShell cmdlet 来启用此功能，你可以自动执行用于访问密钥保管库的 SQL VM 所需的配置。

启用此功能后，它会自动安装 SQL Server 连接器、 配置 EKM 提供程序以访问 Azure 密钥保管库，并创建凭据以使你能够访问你的保管库。 如果你看了前面所述的在本地文档中的步骤，你可以看到此功能可以自动完成步骤 2 和 3。 你仍需要手动执行操作的唯一操作是创建密钥保管库和密钥。 在这里，你的 SQL VM 的整个设置进行自动编辑。 此功能完成此安装程序后，你可以执行 T-SQL 语句，以按照通常加密你的数据库或备份。

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>配置 AKV 集成
使用 PowerShell 来配置 Azure 密钥保管库集成。 下列各节概述了所需的参数，然后一个示例 PowerShell 脚本。

### <a name="install-the-sql-server-iaas-extension"></a>安装 SQL Server IaaS 扩展
首先，[安装 SQL Server IaaS 扩展](../classic/sql-server-agent-extension.md)。

### <a name="understand-the-input-parameters"></a>了解的输入的参数
下表列出在下一节中运行 PowerShell 脚本所需的参数。

| 参数 | 描述 | 示例 |
| --- | --- | --- |
| **$akvURL** |**密钥保管库 URL** |"https://contosokeyvault.vault.azure.net/" |
| **$spName** |**服务主体名称** |"fde2b411-33 d 5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**服务主体密码** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =" |
| **$credName** |**凭据名称**: AKV 集成创建内部 SQL Server，使 VM 具有到密钥保管库的访问权限的凭据。 选择此凭据的名称。 |"mycred1" |
| **$vmName** |**虚拟机名称**： 以前创建的 SQL VM 的名称。 |"myvmname" |
| **$serviceName** |**服务名称**： 与 SQL VM 关联的云服务名称。 |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>启用 AKV 集成使用 PowerShell
**New-azurevmsqlserverkeyvaultcredentialconfig** cmdlet 创建的 Azure 密钥保管库集成功能的配置对象。 **Set-azurevmsqlserverextension**配置与此集成**KeyVaultCredentialSettings**参数。 以下步骤演示如何使用这些命令。

1. 在 Azure PowerShell 中，首先配置输入的参数具有特定值的本主题前面部分中所述。 以下脚本是一个示例。
   
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

SQL IaaS 代理扩展将使用此新配置更新 SQL VM。

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

