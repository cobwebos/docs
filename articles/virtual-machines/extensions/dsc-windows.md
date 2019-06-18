---
title: Azure Desired State Configuration 扩展处理程序 | Microsoft Docs
description: 使用 DSC 扩展在 Azure VM 中上传和应用 PowerShell DSC 配置
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: windows
ms.workload: ''
ms.date: 03/26/2018
ms.author: robreed
ms.openlocfilehash: 9f81e2b7537a5ecc6778baa93a1bab23dd30ff8a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61475501"
---
# <a name="powershell-dsc-extension"></a>PowerShell DSC 扩展

## <a name="overview"></a>概述

适用于 Windows 的 PowerShell DSC 扩展由 Microsoft 发布和提供支持。 扩展在 Azure VM 中上传和应用 PowerShell DSC 配置。 DSC 扩展可调用 PowerShell DSC，在 VM 上启用收到的 DSC 配置。 本文档详细介绍适用于 Windows 的 DSC 虚拟机扩展支持的平台、配置和部署选项。

## <a name="prerequisites"></a>必备组件

### <a name="operating-system"></a>操作系统

DSC 扩展支持以下 OS：

Windows Server 2019、 Windows Server 2016、 Windows Server 2012R2，Windows Server 2012、 Windows Server 2008 R2 SP1、 Windows 客户端 7/8.1/10

### <a name="internet-connectivity"></a>Internet 连接

适用于 Windows 的 DSC 扩展要求目标虚拟机能够与 Azure 以及配置文件包（.zip 文件）的位置（如果它存储在 Azure 之外的位置）通信。 

## <a name="extension-schema"></a>扩展架构

以下 JSON 显示用于 Azure 资源管理器模板中 DSC 扩展的设置部分的架构。 

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false
            "downloadMappings": {
                "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        } 
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>属性值

| 名称 | 值/示例 | 数据类型 |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| publisher | Microsoft.Powershell.DSC | string |
| type | DSC | string |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>设置属性值

| Name | 数据类型 | 描述
| ---- | ---- | ---- |
| settings.wmfVersion | string | 指定应在 VM 上安装的 Windows Management Framework 版本。 将此属性设置为“latest”可安装最新版本的 WMF。 目前，此属性的可能值只有“4.0”、“5.0”和“latest”。 这些可能值将来可能会更新。 默认值为“latest”。 |
| settings.configuration.url | string | 指定要从中下载 DSC 配置 zip 文件的 URL 位置。 如果提供的 URL 需要 SAS 令牌才能访问，必须将 protectedSettings.configurationUrlSasToken 属性设置为 SAS 令牌的值。 如果已定义 settings.configuration.script 和/或 settings.configuration.function，则需要此属性。
| settings.configuration.script | string | 指定包含 DSC 配置定义的脚本的文件名。 此脚本必须位于从 configuration.url 属性所指定的 URL 下载的 zip 文件的根文件夹中。 如果已定义 settings.configuration.url 和/或 settings.configuration.script，则需要此属性。
| settings.configuration.function | string | 指定 DSC 配置的名称。 命名的配置必须包含在 configuration.script 定义的脚本中。 如果已定义 settings.configuration.url 和/或 settings.configuration.function，则需要此属性。
| settings.configurationArguments | Collection | 定义想要传递到 DSC 配置的任何参数。 不会加密此属性。
| settings.configurationData.url | string | 指定 URL，将从中下载配置数据 (.pds1) 文件用作 DSC 配置的输入。 如果提供的 URL 需要 SAS 令牌才能访问，必须将 protectedSettings.configurationDataUrlSasToken 属性设置为 SAS 令牌的值。
| settings.privacy.dataEnabled | string | 启用或禁用遥测数据收集。 此属性的可能值只有“Enable”、“Disable”或“$null”。 将此属性留空，否则 null 将启用遥测
| settings.advancedOptions.forcePullAndApply | Bool | 此设置旨在增强使用扩展将节点注册到 Azure Automation DSC 的体验。  如果值为 `$true`，则扩展会等待从服务拉取的配置完成第一次运行，然后返回成功/失败。  如果值设置为 $false，则扩展返回的状态仅指节点是否已成功注册到 Azure Automation State Configuration，而在注册过程中不会运行节点配置。
| settings.advancedOptions.downloadMappings | Collection | 定义用于下载依赖项（如 WMF 和 .NET）的备用位置

### <a name="protected-settings-property-values"></a>受保护设置属性值

| Name | 数据类型 | 描述
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | string | 定义想要传递到 DSC 配置的任何参数。 将加密此属性。 |
| protectedSettings.configurationUrlSasToken | string | 指定用于访问 configuration.url 所定义的 URL 的 SAS 令牌。 将加密此属性。 |
| protectedSettings.configurationDataUrlSasToken | string | 指定用于访问 configurationData.url 所定义的 URL 的 SAS 令牌。 将加密此属性。 |


## <a name="template-deployment"></a>模板部署

可使用 Azure 资源管理器模板部署 Azure VM 扩展。
部署需要部署后配置的一个或多个虚拟机时，模板是理想选择。
包含 Windows 的 DSC 扩展的示例资源管理器模板可以在 [Azure 快速入门库](https://github.com/Azure/azure-quickstart-templates/blob/master/101-automation-configuration/nested/provisionServer.json#L91)中找到。

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

有关扩展部署状态的数据可以从 Azure 门户和使用 Azure CLI 进行检索。 若要查看给定 VM 的扩展部署状态，请使用 Azure CLI 运行以下命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

下载扩展包并将其部署到 Azure VM 上的此位置
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

扩展状态文件包含子状态和状态成功/错误代码以及每个扩展运行的详细错误和说明。
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

扩展输出日志会记录到以下目录：

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>错误代码及其含义

| 错误代码 | 含义 | 可能的操作 |
| :---: | --- | --- |
| 1000 | 常规错误 | 此错误消息由扩展日志中的特定异常提供 |
| 52 | 扩展安装错误 | 此错误消息由特定异常提供 |
| 1002 | WMF 安装错误 | 安装 WMF 时出错。 |
| 1004 | Zip 包无效 | zip 无效; 解压缩 zip 时出错 |
| 1100 | 参数错误 | 指示用户提供的输入中的问题。 此错误消息由特定异常提供|


### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
