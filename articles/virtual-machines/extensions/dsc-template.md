---
title: Desired State Configuration 扩展与 Azure 资源管理器模板
description: 了解 Azure 中 Desired State Configuration (DSC) 扩展的资源管理器模板定义。
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: dacoulte
ms.openlocfilehash: d19d33d7d8d19923e814b8685c3e56553f85850d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012472"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Desired State Configuration 扩展与 Azure 资源管理器模板

本文介绍 [Desired State Configuration (DSC) 扩展处理程序](dsc-overview.md)的 Azure 资源管理器模板。

> [!NOTE]
> 你可能会遇到略有不同的架构示例。 2016 年 10 月发行版中发生了架构更改。 有关详细信息，请参阅[从以前的格式更新](#update-from-the-previous-format)。

## <a name="template-example-for-a-windows-vm"></a>Windows VM 模板示例

将以下代码片段放入模板的 **Resource** 节。
DSC 扩展继承默认扩展属性。
有关详细信息，请参阅 [VirtualMachineExtension 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension?view=azure-dotnet.)。

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.76",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "protectedSettings": {
                "Items": {
                    "registrationKeyPrivate": "registrationKey"
                }
            },
            "publicSettings": {
                "configurationArguments": [{
                        "Name": "RegistrationKey",
                        "Value": {
                            "UserName": "PLACEHOLDER_DONOTUSE",
                            "Password": "PrivateSettingsRef:registrationKeyPrivate"
                        }
                    },
                    {
                        "RegistrationUrl": "registrationUrl",
                    },
                    {
                        "NodeConfigurationName": "nodeConfigurationName"
                    }
                ]
            }
        }
    }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Windows 虚拟机规模集的模板示例

虚拟机规模集节点具有 **properties** 节，其中包含 **VirtualMachineProfile, extensionProfile** 属性。
在“扩展”下，添加 DSC 扩展的详细信息。

DSC 扩展继承默认扩展属性。
有关详细信息，请参阅 [VirtualMachineScaleSetExtension 类](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension?view=azure-dotnet)。

```json
"extensionProfile": {
    "extensions": [{
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(parameters('VMName'),'/Microsoft.Powershell.DSC')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.Powershell",
            "type": "DSC",
            "typeHandlerVersion": "2.76",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "protectedSettings": {
                    "Items": {
                        "registrationKeyPrivate": "registrationKey"
                    }
                },
                "publicSettings": {
                    "configurationArguments": [{
                            "Name": "RegistrationKey",
                            "Value": {
                                "UserName": "PLACEHOLDER_DONOTUSE",
                                "Password": "PrivateSettingsRef:registrationKeyPrivate"
                            },
                        },
                        {
                            "RegistrationUrl": "registrationUrl",
                        },
                        {
                            "NodeConfigurationName": "nodeConfigurationName"
                        }
                    ]
                }
            },
        }
    }]
}
```

## <a name="detailed-settings-information"></a>详细设置信息

在资源管理器模板 Azure DSC 扩展的 **settings** 节中使用以下架构。

有关可用于默认配置脚本的参数列表，请参阅[默认配置脚本](#default-configuration-script)。

```json
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
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
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
```

## <a name="details"></a>详细信息

| 属性名称 | Type | 说明 |
| --- | --- | --- |
| settings.wmfVersion |字符串 |指定应在 VM 上安装的 Windows Management Framework (WMF) 版本。 将此属性设置为 **latest** 可安装最新版本的 WMF。 目前，此属性的可能值只有 **4.0**、**5.0**、**5.0PP** 和 **latest**。 这些可能值将来可能会更新。 默认值为 **latest**。 |
| settings.configuration.url |字符串 |指定要从中下载 DSC 配置 .zip 文件的 URL 位置。 如果提供的 URL 需要 SAS 令牌才能访问，请将 **protectedSettings.configurationUrlSasToken** 属性设置为 SAS 令牌的值。 如果已定义 **settings.configuration.script** 或 **settings.configuration.function**，则需要此属性。 如果未为这些属性指定任何值，则扩展将调用默认配置脚本设置位置配置管理器 (LCM) 元数据，并应提供参数。 |
| settings.configuration.script |字符串 |指定包含 DSC 配置定义的脚本的文件名。 此脚本必须位于从 **configuration.url** 属性所指定的 URL 下载的 zip 文件的根文件夹中。 如果已定义 **settings.configuration.url** 或 **settings.configuration.script**，则需要此属性。 如果未为这些属性指定任何值，则扩展将调用默认配置脚本设置 LCM 元数据，并应提供参数。 |
| settings.configuration.function |字符串 |指定 DSC 配置的名称。 命名的配置必须包含在 **configuration.script** 定义的脚本中。 如果已定义 **settings.configuration.url** 或 **settings.configuration.function**，则需要此属性。 如果未为这些属性指定任何值，则扩展将调用默认配置脚本设置 LCM 元数据，并应提供参数。 |
| settings.configurationArguments |集合 |定义想要传递到 DSC 配置的任何参数。 此属性未加密。 |
| settings.configurationData.url |字符串 |指定 URL，将从中下载配置数据 (.pds1) 文件用作 DSC 配置的输入。 如果提供的 URL 需要 SAS 令牌才能访问，请将 **protectedSettings.configurationDataUrlSasToken** 属性设置为 SAS 令牌的值。 |
| settings.privacy.dataEnabled |字符串 |启用或禁用遥测数据收集。 此属性的可能值只有 **Enable**、**Disable**、**''** 或 **$null**。 将此属性留空或 null 可启用遥测。 默认值为 **''**。 有关详细信息，请参阅 [Azure DSC 扩展数据集合](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)。 |
| settings.advancedOptions.downloadMappings |集合 |定义要从中下载 WMF 的备选位置。 有关详细信息，请参阅 [Azure DSC 扩展 2.8 以及如何将扩展依赖项下载内容映射到自己的位置](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx)。 |
| protectedSettings.configurationArguments |集合 |定义想要传递到 DSC 配置的任何参数。 此属性已加密。 |
| protectedSettings.configurationUrlSasToken |字符串 |指定用于访问 **configuration.url** 所定义的 URL 的 SAS 令牌。 此属性已加密。 |
| protectedSettings.configurationDataUrlSasToken |字符串 |指定用于访问 **configurationData.url** 所定义的 URL 的 SAS 令牌。 此属性已加密。 |

## <a name="default-configuration-script"></a>默认配置脚本

有关以下值的详细信息，请参阅[本地配置管理器基本设置](/powershell/dsc/metaconfig#basic-settings)。
使用 DSC 扩展默认配置脚本只能配置下表中列出的 LCM 属性。

| 属性名称 | Type | 说明 |
| --- | --- | --- |
| settings.configurationArguments.RegistrationKey |securestring |必需的属性。 指定节点用于注册到 Azure 自动化服务的密钥作为 PowerShell 凭据对象的密码。 可以使用 **listkeys** 方法针对自动化帐户自动发现此值。 该值应作为受保护设置加以保护。 |
| settings.configurationArguments.RegistrationUrl |字符串 |必需的属性。 指定节点将尝试注册的自动化终结点的 URL。 可以使用 **reference** 方法针对自动化帐户自动发现此值。 |
| settings.configurationArguments.NodeConfigurationName |字符串 |必需的属性。 在自动化帐户中指定要分配给节点的节点配置。 |
| settings.configurationArguments.ConfigurationMode |字符串 |指定 LCM 的模式。 有效选项包括 **ApplyOnly**、**ApplyandMonitor** 和 **ApplyandAutoCorrect**。  默认值为 **ApplyandMonitor**。 |
| settings.configurationArguments.RefreshFrequencyMins | uint32 | 指定 LCM 将尝试向自动化帐户查询更新的频率。  默认值为 **30**。  最小值为 **15**。 |
| settings.configurationArguments.ConfigurationModeFrequencyMins | uint32 | 指定 LCM 将验证当前配置的频率。 默认值为 **15**。 最小值为 **15**。 |
| settings.configurationArguments.RebootNodeIfNeeded | 布尔值 | 指定在 DSC 操作请求时是否可能会自动重新启动节点。 默认值为“false”。 |
| settings.configurationArguments.ActionAfterReboot | 字符串 | 指定在应用配置时重新启动后会发生什么情况。 有效选项为 **ContinueConfiguration** 和 **StopConfiguration**。 默认值为 **ContinueConfiguration**。 |
| settings.configurationArguments.AllowModuleOverwrite | 布尔值 | 指定 LCM 是否会覆盖节点上的现有模块。 默认值为“false”。 |

## <a name="settings-vs-protectedsettings"></a>Settings 与ProtectedSettings

所有设置保存在 VM 上的 settings 文本文件中。
**settings** 下面列出的属性是公共属性。
公共属性未在 settings 文本文件中加密。
**protectedSettings** 下面列出的属性已使用证书加密，因此不会在 VM 上的 settings 文件中以明文显示。

如果配置需要凭据，可将凭据包含在 **protectedSettings** 中：

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>示例配置脚本

以下示例演示 DSC 扩展的默认行为，其目的是为 LCM 提供元数据设置，并注册到 Automation DSC 服务。
必须指定配置参数。
将配置参数传递给默认配置脚本以设置 LCM 元数据。

```json
"settings": {
    "configurationArguments": {
        {
            "Name": "RegistrationKey",
            "Value": {
                "UserName": "PLACEHOLDER_DONOTUSE",
                "Password": "PrivateSettingsRef:registrationKeyPrivate"
            },
        },
        "RegistrationUrl": "[parameters('registrationUrl1')]",
        "NodeConfigurationName": "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "Items": {
        "registrationKeyPrivate": "[parameters('registrationKey1']"
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>在 Azure 存储中使用配置脚本的示例

以下示例摘自 [DSC 扩展处理程序概述](dsc-overview.md)。
此示例使用 Resource Manager 模板而不是cmdlet 来部署该扩展。
保存 IisInstall.ps1 配置，将它放在 .zip 文件中，并将该文件上传到可访问的 URL 中。
此示例使用 Azure Blob 存储，但可以从任意位置下载 .zip 文件。

在资源管理器模板中，以下代码指示 VM 下载正确的文件并运行适当的 PowerShell 函数：

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="update-from-a-previous-format"></a>从以前的格式更新

以前的扩展格式中的所有设置（包含 **ModulesUrl**、**ConfigurationFunction**、**SasToken** 或 **Properties** 公共属性）会自动调整为当前扩展格式。
它们按以前的相同方式运行。

以下架构显示了以前的 settings 架构的大致形式：

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

以前的格式调整为当前格式后的情况如下所示：

| 属性名称 | 以前架构的等效值 |
| --- | --- |
| settings.wmfVersion |settings.WMFVersion |
| settings.configuration.url |settings.ModulesUrl |
| settings.configuration.script |settings.ConfigurationFunction 的第 1 个部分（在 \\\\ 前面） |
| settings.configuration.function |settings.ConfigurationFunction 的第 2 个部分（在 \\\\ 后面） |
| settings.configurationArguments |settings.Properties |
| settings.configurationData.url |protectedSettings.DataBlobUri（没有 SAS 令牌） |
| settings.privacy.dataEnabled |settings.Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings |settings.AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken |settings.SasToken |
| protectedSettings.configurationDataUrlSasToken |protectedSettings.DataBlobUri 中的 SAS 令牌 |

## <a name="troubleshooting---error-code-1100"></a>故障排除 - 错误代码 1100

错误代码 1100 指示 DSC 扩展中的用户输入有问题。
这些错误的文本是可变的，将来可能会更改。
下面是可能会遇到的一些错误及其解决方法。

### <a name="invalid-values"></a>无效值

“Privacy.dataCollection 为‘{0}’。
可能的值只有 ''、'Enable' 和 'Disable'”。
“WmfVersion 为‘{0}’。
唯一的可能值为 ... 和 'latest'”。

**问题**：不允许使用提供的值。

**解决方法**：将无效值更改为有效值。
有关详细信息，请参阅[详细信息](#details)中的表格。

### <a name="invalid-url"></a>无效的 URL

“ConfigurationData.url 为‘{0}’。 这不是有效的 URL”。“DataBlobUri 为‘{0}’。 这不是有效的 URL”。“Configuration.url 为‘{0}’。 这不是有效的 URL”

**问题**：提供的 URL 无效。

**解决方法**：检查提供的所有 URL。
确保所有 URL 都解析为扩展可在远程计算机上访问的有效位置。

### <a name="invalid-configurationargument-type"></a>无效的 ConfigurationArgument 类型

“无效的 configurationArguments 类型 {0}”

**问题**：*ConfigurationArguments* 属性无法解析为 **Hashtable** 对象。

**解决方法**：将 *ConfigurationArguments* 属性设置为 **Hashtable**。
遵循上述示例中提供的格式。 请注意引号、逗号和括号。

### <a name="duplicate-configurationarguments"></a>重复的 ConfigurationArguments

“在公共和受保护的 configurationArguments 中发现重复的参数‘{0}’”

**问题**：公共设置中的 *ConfigurationArguments* 和受保护设置中的 *ConfigurationArguments* 包含同名属性。

**解决方法**：删除其中一个重复的属性。

### <a name="missing-properties"></a>缺少属性

“Configuration.function 要求指定 configuration.url 或 configuration.module”

“Configuration.url 要求指定 configuration.script”

“Configuration.script 要求指定 configuration.url”

“Configuration.url 要求指定 configuration.function”

“ConfigurationUrlSasToken 要求指定 configuration.url”

“ConfigurationDataUrlSasToken 要求指定 configurationData.url”

**问题**：定义的属性需要另一个缺少的属性。

**解决方法**：

- 提供缺少的属性。
- 删除需要缺失属性的属性。

## <a name="next-steps"></a>后续步骤

- 了解如何[将虚拟机规模集与 Azure DSC 扩展配合使用](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)。
- 了解有关 [DSC 的安全凭据管理](dsc-credentials.md)的更多详细信息。
- 获取 [Azure DSC 扩展处理程序简介](dsc-overview.md)。
- 有关 PowerShell DSC 的详细信息，请转到 [PowerShell 文档中心](/powershell/dsc/overview)。
