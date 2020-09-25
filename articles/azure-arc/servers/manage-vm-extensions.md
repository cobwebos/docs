---
title: 启用了 Azure Arc 的服务器的 VM 扩展管理
description: 启用 Azure Arc 的服务器可以管理虚拟机扩展的部署，这些扩展提供部署后配置和自动化任务和非 Azure Vm。
ms.date: 09/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1c3d50f407f4412a14201dfe669334dbb083d323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329068"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers"></a>启用了 Azure Arc 服务器的虚拟机扩展管理

虚拟机 (VM) 扩展是小型应用程序，用于在 Azure Vm 上提供部署后配置和自动化任务。 例如，如果某个虚拟机需要安装软件、防病毒保护或运行脚本，便可以使用 VM 扩展。

使用启用了 azure Arc 的服务器，可以将 Azure VM 扩展部署到非 Azure Windows 和 Linux Vm，从而简化混合计算机在本地、边缘和其他云环境中的生命周期管理。

## <a name="key-benefits"></a>主要优点

支持 Azure Arc 的服务器 VM 扩展支持提供以下主要优势：

* 使用 [Azure 自动化状态配置](../../automation/automation-dsc-overview.md) 集中存储配置，并通过 DSC VM 扩展维护启用混合连接的计算机的所需状态。

* 收集日志数据以便通过 Log Analytics 代理 VM 扩展启用 [Azure Monitor 中的日志](../../azure-monitor/platform/data-platform-logs.md) 进行分析。 这对于跨各种源中的数据执行复杂分析非常有用。

* 在 [用于 VM 的 Azure Monitor](../../azure-monitor/insights/vminsights-overview.md)中，会分析 Windows 和 Linux vm 的性能，并监视其进程和其他资源和外部进程的依赖项。 这是通过同时启用 Log Analytics 代理和依赖项代理 VM 扩展来实现的。

* 使用自定义脚本扩展在混合连接的计算机上下载和执行脚本。 此扩展适用于部署后配置、软件安装或其他任何配置或管理任务。

## <a name="availability"></a>可用性

VM 扩展功能仅在 [受支持区域](overview.md#supported-regions)的列表中提供。 确保你在其中一个区域中载入计算机。

## <a name="extensions"></a>扩展

在此版本中，我们在 Windows 和 Linux 计算机上支持以下 VM 扩展。

|扩展 |OS |发布者 |其他信息 |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Windows 自定义脚本扩展](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft PowerShell|[Windows PowerShell DSC 扩展](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics 代理 |Windows |Microsoft.EnterpriseCloud.Monitoring |[适用于 Windows 的 Log Analytics VM 扩展](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft 依赖项代理 | Windows |Microsoft.Compute | [适用于 Windows 的依赖关系代理虚拟机扩展](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft Azure 扩展 |[Linux 自定义脚本扩展版本2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[适用于 Linux 的 PowerShell DSC 扩展](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics 代理 |Linux |Microsoft.EnterpriseCloud.Monitoring |[适用于 Linux 的 Log Analytics VM 扩展](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft 依赖项代理 | Linux |Microsoft.Compute | [适用于 Linux 的依赖关系代理虚拟机扩展](../../virtual-machines/extensions/agent-dependency-linux.md) |

VM 扩展可使用 Azure 资源管理器模板、Azure 门户或 Azure PowerShell 启用了 Arc 的服务器管理的混合服务器运行。

若要了解有关 Azure 连接的计算机代理包以及有关扩展代理组件的详细信息，请参阅 [代理概述](agent-overview.md#agent-component-details)。

## <a name="prerequisites"></a>先决条件

此功能依赖于订阅中的以下 Azure 资源提供程序：

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

如果尚未注册，请按照 [注册 Azure 资源提供程序](agent-overview.md#register-azure-resource-providers)中的步骤进行操作。

适用于 Linux 的 Log Analytics 代理 VM 扩展需要在目标计算机上安装 Python 2.x。

### <a name="connected-machine-agent"></a>Connected Machine 代理

验证计算机是否与支持 Azure 连接的计算机代理的 Windows 和 Linux 操作系统的 [支持版本](agent-overview.md#supported-operating-systems) 相匹配。

Windows 和 Linux 上此功能支持的已连接计算机代理的最低版本为1.0 版。

若要将计算机升级到所需的代理版本，请参阅 [升级代理](manage-agent.md#upgrading-agent)。

## <a name="enable-extensions-from-the-portal"></a>从门户启用扩展

VM 扩展可通过 Azure 门户应用服务器托管计算机的 Arc。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到 " **服务器-Azure Arc** "，然后从列表中选择你的混合计算机。

3. 选择 " **扩展**"，然后选择 " **添加**"。 从可用扩展的列表中选择所需扩展，并按向导中的说明操作。 在此示例中，我们将部署 Log Analytics VM 扩展。

    ![选择所选计算机的 VM 扩展](./media/manage-vm-extensions/add-vm-extensions.png)

    下面的示例演示如何从 Azure 门户安装 Log Analytics VM 扩展：

    ![安装 Log Analytics VM 扩展](./media/manage-vm-extensions/mma-extension-config.png)

    若要完成安装，你需要提供工作区 ID 和主密钥。 如果你不熟悉如何查找此信息，请参阅 [获取工作区 ID 和密钥](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)。

4. 确认提供所需信息后，选择 " **创建**"。 将显示部署的摘要，你可以查看部署的状态。

>[!NOTE]
>虽然可以将多个扩展组合在一起并进行处理，但它们是以串行方式安装的。 第一次扩展安装完成后，将尝试安装下一扩展。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager 模板

VM 扩展可添加到 Azure Resource Manager 模板，并在部署模板的过程中执行。 使用启用了 Arc 的服务器支持的 VM 扩展，可以使用 Azure PowerShell 在 Linux 或 Windows 计算机上部署受支持的 VM 扩展。 下面的每个示例都包含模板文件和参数文件，其中包含要提供给模板的示例值。

>[!NOTE]
>虽然可以将多个扩展组合在一起并进行处理，但它们是以串行方式安装的。 第一次扩展安装完成后，将尝试安装下一扩展。

### <a name="deploy-the-log-analytics-vm-extension"></a>部署 Log Analytics VM 扩展

为了轻松部署 Log Analytics 代理，提供了以下示例以在 Windows 或 Linux 上安装代理。

#### <a name="template-file-for-linux"></a>适用于 Linux 的模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>适用于 Windows 的模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>参数文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

将模板和参数文件保存到磁盘，然后用部署的适当值编辑参数文件。 然后，可以使用以下命令在资源组中的所有连接的计算机上安装该扩展。 该命令使用 *TemplateFile* 参数来指定模板，并使用 *TemplateParameterFile* 参数指定包含参数和参数值的文件。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>部署自定义脚本扩展

若要使用自定义脚本扩展，提供了以下示例以在 Windows 和 Linux 上运行。 如果不熟悉自定义脚本扩展，请参阅适用于 [Windows 的自定义脚本扩展](../../virtual-machines/extensions/custom-script-windows.md) 或 [适用于 Linux 的自定义脚本扩展](../../virtual-machines/extensions/custom-script-linux.md)。 对混合计算机使用此扩展时，有几个不同的特征需要了解：

* 具有 Azure VM 自定义脚本扩展的支持的操作系统列表不适用于启用了 Azure Arc 的服务器。 可在 [此处](agent-overview.md#supported-operating-systems)找到启用了 Arc 的服务器的支持 OSs 列表。

* 有关 Azure 虚拟机规模集或经典 Vm 的配置详细信息不适用。

* 如果你的计算机需要在外部下载脚本并且只能通过代理服务器进行通信，则需要 [配置已连接的计算机代理](manage-agent.md#update-or-remove-proxy-settings) 以设置代理服务器环境变量。

自定义脚本扩展配置指定脚本位置和要运行命令等设置。 此配置在 Azure 资源管理器模板中指定，如下所示，适用于 Linux 和 Windows 混合计算机。

#### <a name="template-file-for-linux"></a>适用于 Linux 的模板文件

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>适用于 Windows 的模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>部署 PowerShell DSC 扩展

若要使用 PowerShell DSC 扩展，提供了以下示例以在 Windows 和 Linux 上运行。 如果不熟悉 PowerShell DSC 扩展，请参阅 [DSC 扩展处理程序概述](../../virtual-machines/extensions/dsc-overview.md)。 对混合计算机使用此扩展时，有几个不同的特征需要了解：

* Azure VM PowerShell DSC 扩展支持的操作系统列表不适用于启用了 Azure Arc 的服务器。 可在 [此处](agent-overview.md#supported-operating-systems)找到启用了 Arc 的服务器的支持 OSs 列表。

* 如果你的计算机需要在外部下载脚本并且只能通过代理服务器进行通信，则需要 [配置已连接的计算机代理](manage-agent.md#update-or-remove-proxy-settings) 以设置代理服务器环境变量。

#### <a name="template-file-for-linux"></a>适用于 Linux 的模板文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>适用于 Windows 的模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>依赖关系代理

若要使用 Azure Monitor 依赖关系代理扩展，提供以下示例以在 Windows 和 Linux 上运行。 如果不熟悉依赖关系代理，请参阅 [Azure Monitor 代理概述](../../azure-monitor/platform/agents-overview.md#dependency-agent)。

#### <a name="template-file-for-linux"></a>适用于 Linux 的模板文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>适用于 Windows 的模板文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="uninstall-extension"></a>卸载扩展

从启用 Arc 的服务器中删除一个或多个扩展只能从 Azure 门户执行。 执行以下步骤以删除扩展。

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

2. 在门户中，浏览到 " **服务器-Azure Arc** "，然后从列表中选择你的混合计算机。

3. 选择 " **扩展**"，然后从已安装的扩展列表中选择一个扩展。

4. 选择 " **卸载** "，并在出现提示时选择 **"是"** 以继续。

## <a name="next-steps"></a>后续步骤

* 疑难解答信息可在 [VM 扩展疑难解答指南](troubleshoot-vm-extensions.md)中找到。

* 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区报告，使用[用于 VM 的 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md) 启用监视等。

* 详细了解 [Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 需要收集操作系统和工作负荷监视数据、使用自动化 runbook 或功能（如更新管理）管理该数据，或使用 [Azure 安全中心](../../security-center/security-center-intro.md)之类的其他 azure 服务时，需要使用适用于 Windows 和 Linux 的 Log Analytics 代理。