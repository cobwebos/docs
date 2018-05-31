---
title: 使用所需状态配置将凭据传递给 Azure
description: 了解如何使用 PowerShell 所需状态配置 (DSC) 安全地将凭据传递给 Azure 虚拟机。
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: 666253d16ac51dcc21174211f71794f8b0ede07d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012540"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>将凭据传递给 Azure DSC 扩展处理程序

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

本文介绍了 Azure 的所需状态配置 (DSC) 扩展。 有关 DSC 扩展处理程序的概述，请参阅 [Azure 所需状态配置扩展处理程序](dsc-overview.md)。

## <a name="pass-in-credentials"></a>传入凭据

在配置过程中，可能需要在用户上下文中设置用户帐户、访问服务或安装程序。 若要执行这些操作，需提供凭据。

可以使用 DSC 来设置参数化配置。 在参数化配置中，凭据将传递到配置并安全地存储在 .mof 文件中。 Azure 扩展处理程序提供证书的自动管理功能，以此简化凭据管理。

以下 DSC 配置脚本创建具有指定密码的本地用户帐户：

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

必须将 **node localhost** 包含为配置的一部分。 扩展处理程序会特意查找 **node localhost** 语句。 如果缺少此语句，则以下步骤不起作用。 还必须包含类型强制转换 **[PsCredential]**。 此特定类型触发扩展对凭据进行加密。

将此脚本发布到 Azure Blob 存储：

`Publish-AzureRmVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

设置 Azure DSC 扩展并提供凭据：

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzureRmVM -Name 'example-1'

$vm = Set-AzureRmVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureRmVM
```

## <a name="how-a-credential-is-secured"></a>如何保护凭据

运行此代码时会出现输入凭据的提示。 提供凭据后，它短暂地存储在内存中。 使用 **Set-AzureRmVMDscExtension** cmdlet 发布凭据时，会通过 HTTPS 将凭据传输到 VM。 在 VM 中，Azure 使用本地 VM 证书将加密的凭据存储在磁盘上。 若要将凭据传递给 DSC，将在内存中短暂地将其解密，然后将其重新加密。

此过程不同于[使用不带扩展处理程序的安全配置](/powershell/dsc/securemof)。 Azure 环境提供了通过证书安全地传输配置数据的方法。 使用 DSC 扩展处理程序时，无需在 **ConfigurationData** 中提供 **$CertificatePath** 或 **$CertificateID**/ **$Thumbprint** 条目。

## <a name="next-steps"></a>后续步骤

- 获取 [Azure DSC 扩展处理程序简介](dsc-overview.md)。
- 检查[适用于 DSC 扩展的 Azure 资源管理器模板](dsc-template.md)。
- 有关 PowerShell DSC 的详细信息，请转到 [PowerShell 文档中心](/powershell/dsc/overview)。
- 若要了解可以使用 PowerShell DSC 管理的其他功能并获取更多 DSC 资源，请浏览 [PowerShell 库](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)。