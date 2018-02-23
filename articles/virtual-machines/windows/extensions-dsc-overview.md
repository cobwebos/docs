---
title: "适用于 Azure 的 Desired State Configuration 概述 | Microsoft Docs"
description: "有关使用 PowerShell Desired State Configuration 的 Microsoft Azure 扩展的概述。 内容涉及先决条件、体系结构和 cmdlet。"
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure Desired State Configuration 扩展处理程序简介

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure VM 代理和关联的扩展是 Microsoft Azure 基础结构服务的一部分。
VM 扩展是软件组件，可以扩展 VM 功能并简化各种 VM 管理操作。

Desired State Configuration 扩展的主要用例是对 [Azure Automation DSC 服务](../../automation/automation-dsc-overview.md)启动 VM，这样可以带来[好处](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service)，包括持续管理 VM 配置和集成 Azure 监视等其他操作工具。

它还可以独立于 Azure Automation DSC 服务使用 DSC 扩展，但这是在部署期间发生的单次操作，不会持续报告和管理 VM 本地外的配置。

本文的内容涵盖两种方案、用于 Azure 自动化载入的 DSC 扩展和如何使用 DSC 扩展作为通过 Azure SDK 将配置分配给虚拟机的工具。

## <a name="prerequisites"></a>先决条件

- **本地计算机** - 若要与 Azure VM 扩展交互，需要使用 Azure 门户或 Azure PowerShell SDK。
- **来宾代理** - 使用 DSC 配置进行配置的 Azure VM 需是支持 Windows Management Framework (WMF) 4.0 或更高版本的 OS。 有关支持的 OS 版本的完整列表，请参阅 [DSC Extension Version History](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/)（DSC 扩展版本历史记录）页面。

## <a name="terms-and-concepts"></a>术语和概念

本指南假设熟悉以下概念：

- **配置** - DSC 配置文档。
- **节点** -DSC 配置的目标。 本文档中，“节点”一律指 Azure VM。
- **配置数据** - 包含配置环境数据的 psd1 文件。

## <a name="architectural-overview"></a>体系结构概述

Azure DSC 扩展使用 Azure VM 代理框架来传送、启用和报告 Azure VM 上运行的 DSC 配置。
DSC 扩展接受配置文档和一组参数。
如果未提供任何文件，[默认配置脚本](###default-configuration-script)会嵌入仅用于在[本地配置管理器](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig)中设置元数据的扩展。

首次调用扩展时，该扩展使用以下逻辑安装 Windows Management Framework (WMF) 版本：

1. 如果 Azure VM OS 是 Windows Server 2016，则不执行任何操作。 Windows Server 2016 上已安装最新版本的 PowerShell。
2. 如果已指定 `wmfVersion` 属性，除非该 WMF 版本与 VM 的 OS 不兼容，否则将安装该版本。
3. 如果未指定 `wmfVersion` 属性，则安装 WMF 的最新适用版本。

安装 WMF 需要重新启动。
重新启动后，扩展将下载 `modulesUrl` 属性中指定的 .zip 文件（若提供）。
如果此位置是在 Azure Blob 存储中，则可以在 `sasToken` 属性中指定 SAS 令牌来访问该文件。
下载并解压缩 .zip 之后，将运行 `configurationFunction` 中定义的配置函数以生成 MOF 文件。
然后，扩展使用生成的 MOF 文件运行 `Start-DscConfiguration -Force`。
扩展将捕获输出并将其写入 Azure 状态通道。

### <a name="default-configuration-script"></a>默认配置脚本

Azure DSC 扩展包括一个默认配置脚本，该脚本计划在对 Azure Automation DSC 服务载入 VM 时使用。
脚本参数符合[本地配置管理器](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig)的可配置属性。
[记录了](extensions-dsc-template.md##default-configuration-script)脚本参数，[GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true) 中提供完整的脚本。

## <a name="dsc-extension-in-arm-templates"></a>ARM 模板中的 DSC 扩展

在大多数情况下使用 DSC 扩展时一般都会使用 Azure 资源管理器 (ARM) 部署模板。
有关在 ARM 部署模板中包括 DSC 扩展的信息和示例，请参阅专用文档页[Desired State Configuration 扩展与 Azure 资源管理器模板](extensions-dsc-template.md)

## <a name="dsc-extension-powershell-cmdlets"></a>DSC 扩展 PowerShell Cmdlet

用于管理 DSC 扩展的 PowerShell cmdlet 最适合用于交互式故障排除和信息收集方案。
cmdlet 可用于打包、发布和监视 DSC 扩展部署。
请注意，DSC 扩展的 cmdlet 尚未更新，无法使用[默认配置脚本](###default-configuration-script)。

`Publish-AzureRMVMDscConfiguration` 检索配置文件，扫描其中是否有依赖的 DSC 资源，并创建一个 .zip 文件，其中包含启用配置所需的配置和 DSC 资源。
它还可以使用 `-ConfigurationArchivePath` 参数在本地创建包。
否则，它会将 .zip 文件发布到 Azure Blob 存储，然后使用 SAS 令牌保护该文件。

在此 cmdlet 创建的 .zip 文件中，存档文件夹根目录处提供了 .ps1 配置脚本。
资源会将模块文件夹放置在存档文件夹中。

`Set-AzureRMVMDscExtension` 将 PowerShell DSC 扩展所需的设置注入 VM 配置对象。

`Get-AzureRMVMDscExtension` 可检索特定 VM 的 DSC 扩展状态。

`Get-AzureRMVMDscExtensionStatus` 可检索由 DSC 扩展处理程序启用的 DSC 配置的状态。
可以在一个或一组 VM 上执行此操作。

`Remove-AzureRMVMDscExtension` 可从给定的虚拟机中删除扩展处理程序。
此 cmdlet **不会**删除配置、卸载 WMF 或更改虚拟机上已应用的设置。
而只删除扩展处理程序。 

有关 AzureRM DSC 扩展 cmdlet 的重要信息：

- Azure 资源管理器 cmdlet 是同步的。
- ResourceGroupName、VMName、ArchiveStorageAccountName、Version 和 Location 都是必需参数。
- ArchiveResourceGroupName 是可选参数。 如果用户的存储帐户所属的资源组与创建虚拟机的资源组不同，用户可以指定此参数。
- AutoUpdate 开关可以在有最新版本时将扩展处理程序自动更新为最新版本。 请注意，当发布了新版本的 WMF 时，此参数可能会导致 VM 重新启动。

### <a name="getting-started-with-cmdlets"></a>Cmdlet 入门

Azure DSC 扩展能够在部署过程中直接使用 DSC 配置文档配置 Azure VM，但不会将节点注册到 Azure 自动化，因此不会集中管理该节点。

下面是一个简单的配置示例。
以“IisInstall.ps1”名称将其保存在本地：

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

以下步骤将 IisInstall.ps1 脚本放在指定的 VM 上，执行配置，然后报告状态。

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Azure 门户功能

浏览到 VM。 在“设置”->“常规”下面，单击“扩展”。
此时会创建一个新窗格。
单击“添加”，并选择“PowerShell DSC”。

门户需要输入。
**配置模块或脚本**：这是必填字段（[默认配置脚本](###default-configuration-script)的窗体尚未更新。
需要一个包含配置脚本的 .ps1 文件，或者需要一个 .zip 文件，其中的 .ps1 配置脚本位于根目录，所有依赖资源位于模块文件夹。
可以使用 Azure PowerShell SDK 随附的 `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet 来创建该文件。
系统会将 .zip 文件上传到受 SAS 令牌保护的用户 Blob 存储。

**配置数据 PSD1 文件**：这是可选字段。
如果配置要求 .psd1 中有配置数据文件，请使用此字段来进行选择，然后将它上传到受 SAS 令牌保护的用户 Blob 存储。

**配置的模块限定名称**：.ps1 文件可以包含多个配置函数。
请输入配置 .ps1 脚本的名称，后面再加上 '\' 和配置函数的名称。
例如，如果 .ps1 脚本的名称为“configuration.ps1”，而配置为“IisInstall”，则可输入：`configuration.ps1\IisInstall`

**配置参数**：如果配置函数采用参数，请使用 `argumentName1=value1,argumentName2=value2` 格式在此处输入。
请注意，此格式与通过 PowerShell cmdlet 或 Resource Manager 模板接受配置参数的方式不同。

## <a name="logging"></a>日志记录
日志位于：

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>后续步骤
有关 PowerShell DSC 的详细信息，请[访问 PowerShell 文档中心](https://msdn.microsoft.com/powershell/dsc/overview)。 

检查[适用于 DSC 扩展的 Azure 资源管理器模板](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

若要查找可以使用 PowerShell DSC 管理的其他功能，请[浏览 PowerShell 库](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)获取更多 DSC 资源。

有关将敏感参数传入配置的详细信息，请参阅 [Manage credentials securely with the DSC extension handler](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（使用 DSC 扩展处理程序安全管理凭据）。
