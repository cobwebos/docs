---
title: 适用于 Windows 的 Azure 性能诊断 VM 扩展 | Microsoft 文档
description: 介绍适用于 Windows 的 Azure 性能诊断 VM 扩展。
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: 3e2f8be0f77e220da483dcfb18d6b324d3f203ed
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>适用于 Windows 的 Azure 性能诊断 VM 扩展

Azure 性能诊断 VM 扩展可用于从 Windows VM 收集性能诊断数据。 此扩展可以执行分析，并提供一个报告，其中的发现和建议可用于确定和解决虚拟机上的性能问题。 此扩展将安装被称为 [PerfInsights](http://aka.ms/perfinsights) 的疑难解答工具。

## <a name="prerequisites"></a>先决条件

此扩展可以安装在 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016 上。 它还可以安装在 Windows 8.1 和 Windows 10 上。

## <a name="extension-schema"></a>扩展架构
以下 JSON 显示了 Azure 性能诊断 VM 扩展的架构。 此扩展需要存储帐户的名称和密钥来存储诊断输出和报告。 这些值很敏感。 存储帐户密钥应存储在受保护的设置配置中。 Azure VM 扩展保护的设置数据已加密，并且只能在目标虚拟机上解密。 请注意，**storageAccountName** 和 **storageAccountKey** 区分大小写。 以下部分列出了其他必需参数。

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>属性值

|   **Name**   |**值/示例**|       **说明**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|API 的版本。
|发布者|Microsoft.Azure.Performance.Diagnostics|扩展的发布服务器命名空间。
|type|AzurePerformanceDiagnostics|VM 扩展的类型。
|typeHandlerVersion|1.0|扩展处理程序的版本。
|performanceScenario|基本|需为其捕获数据的性能方案。 有效值为：“基本”、“vmslow”、“azurefiles”和“自定义”。
|traceDurationInSeconds|300|在选择任意跟踪选项的情况下的跟踪持续时间。
|perfCounterTrace|p|启用性能计数器跟踪的选项。 有效值为“p”或空值。 如果不希望捕获此跟踪，请将该值留空。
|networkTrace|n|启用网络跟踪的选项。 有效值为 n 或空值。 如果不希望捕获此跟踪，请将该值留空。
|xperfTrace|x|启用 XPerf 跟踪的选项。 有效值为“x”或空值。 如果不希望捕获此跟踪，请将该值留空。
|storPortTrace|s|启用 StorPort 跟踪的选项。 有效值为 **s** 或空值。 如果不希望捕获此跟踪，请将该值留空。
|srNumber|123452016365929|支持票证编号（如果有）。 将此值留空（如果没有此值）。
|requestTimeUtc|2017-09-28T22:08:53.736Z|UTC 格式的当前日期时间。 如果使用门户来安装此扩展，则不需要提供此值。
|storageAccountName|mystorageaccount|用于存储诊断日志和结果的存储帐户名称。
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|存储帐户的密钥。

## <a name="install-the-extension"></a>安装扩展

请按照这些说明在 Windows 虚拟机上安装扩展：

1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 选择你想要安装此扩展的虚拟机。

    ![Azure 门户的屏幕截图，其中突出显示了虚拟机](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. 选择“扩展”边栏选项卡，然后选择“添加”。

    ![“扩展”边栏选项卡的屏幕截图，其中突出显示了“添加”](media/performance-diagnostics-vm-extension/select-extensions.png)
4. 选择“Azure 性能诊断”，查看条款和条件，然后选择“创建”。

    ![“新建资源”屏幕的屏幕截图，其中突出显示了“Azure 性能诊断”](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. 提供用于安装的参数值，然后选择“确定”安装扩展。 有关支持的方案的详细信息，请参阅[如何使用 PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios)。 

    ![“安装扩展”对话框的屏幕截图](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. 安装成功后，会看到一条指示此状态的消息。

    ![“预配成功”消息的屏幕截图](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > 扩展在预配成功后运行。 对于基本方案，至多需要两分钟即可完成。 对于其他方案，将在安装过程中指定的持续时间内运行。

## <a name="remove-the-extension"></a>删除扩展
若要从虚拟机中删除该扩展，请按照下列步骤操作：

1. 登录到 [Azure 门户](http://portal.azure.com)，选择要在其中删除此扩展的虚拟机，然后选择“扩展”边栏选项卡。 
2. 选择 (...)，从列表中找到“性能诊断扩展”条目，然后选择“卸载”。

    ![“扩展”边栏选项卡的屏幕截图，其中突出显示了“卸载”](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > 此外，也可先选择扩展条目，然后选择“卸载”选项。

## <a name="template-deployment"></a>模板部署
可使用 Azure 资源管理器模板部署 Azure 虚拟机扩展。 在前一部分详述的 JSON 架构可以用在 Azure 资源管理器模板中。 这样就可以在 Azure 资源管理器模板部署期间运行 Azure 性能诊断 VM 扩展。 下面是示例模板：

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
        "protectedSettings": {            
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell 部署
可以使用 `Set-AzureRmVMExtension` 命令将 Azure 性能诊断 VM 扩展部署到现有的虚拟机。

PowerShell

````
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
````

## <a name="information-on-the-data-captured"></a>有关捕获的数据信息
PerfInsights 工具将收集各种日志、配置和诊断数据，具体视所选方案而定。 有关详细信息，请参阅 [PerfInsights 文档](http://aka.ms/perfinsights)。

## <a name="view-and-share-the-results"></a>查看和共享结果

扩展的输出可以在上传到安装期间指定的存储帐户的 zip 文件中找到，并使用[共享访问签名 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 共享 30 天。 此 zip 文件包含诊断日志和一个包含发现和建议的报告。 输出 zip 文件的 SAS 链接可以在 **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\\\<version>** 文件夹下名为 *zipfilename*_saslink.txt 的文本文件中找到。 拥有此链接的任何人都可以下载 zip 文件。

Microsoft 可能会使用此 SAS 链接下载诊断数据，为从事票证支持工作的支持工程师提供帮助。

若要查看报告，请解压缩 zip 文件，然后打开 **PerfInsights Report.html** 文件。

应该也可以通过选择该扩展直接从门户下载 zip 文件。

![性能诊断详细状态的屏幕截图](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> 有时，门户中显示的 SAS 链接可能无法使用。 这可能是由编码和解码操作中出现的 URL 格式破坏造成的。 可以改为直接从 VM 的 *_saslink.tx 文件获取链接。

## <a name="troubleshoot-and-support"></a>故障排除和支持

- 即使已成功预配扩展，（通知区域中的）扩展部署状态可能仍会显示为“正在进行部署”。

    只要扩展状态指示已成功预配扩展，就可以放心忽略此问题。
- 安装过程中的某些问题可使用扩展日志来解决。 扩展执行输出将记录到在以下目录中发现的文件：

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
