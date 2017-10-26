---
title: "适用于 Windows 的 Azure 性能诊断 VM 扩展 | Microsoft 文档"
description: "介绍适用于 Windows 的 Azure 性能诊断 VM 扩展。"
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: 85d4764534c77ea0e4d999e249abe456d0234d75
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>适用于 Windows 的 Azure 性能诊断 VM 扩展

## <a name="summary"></a>摘要
Azure 性能诊断 VM 扩展可帮助从 Windows VM 收集性能诊断数据，执行分析，并提供发现和建议的报告，以识别并解决虚拟机上的性能问题。 此扩展将安装被称为 [PerfInsights](http://aka.ms/perfinsights) 的疑难解答工具。

## <a name="prerequisites"></a>先决条件
### <a name="operating-systems"></a>操作系统
此扩展可以安装在 Windows Server 2008 R2、2012、2012 R2、2016；Windows 8.1 和 Windows 10 操作系统上。

## <a name="extension-schema"></a>扩展架构
以下 JSON 显示了 Azure 性能诊断扩展的架构。 此扩展需要存储帐户的名称和密钥来存储诊断输出和报告。 这些值很敏感，应存储在受保护的设置配置中。 Azure VM 扩展保护的设置数据已加密，并且只能在目标虚拟机上解密。 请注意，storageAccountName 和 storageAccountKey 是区分大小写的。 下面部分中列出了其他所需的参数。

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
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>属性值

|   **Name**   |**值/示例**|       **说明**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|API 的版本
|发布者|Microsoft.Azure.Performance.Diagnostics|扩展的发布服务器命名空间
|type|AzurePerformanceDiagnostics|VM 扩展的类型
|typeHandlerVersion|1.0|扩展处理程序的版本
|performanceScenario|基本|要捕获的数据适用的性能方案。 有效值为：“基本”、“vmslow”、“azurefiles”和“自定义”。
|traceDurationInSeconds|300|如果选择了任何跟踪选项的跟踪持续时间。
|DiagnosticsTrace|d|启用诊断跟踪的选项。 有效值为“d”或空值。 如果不希望捕获此跟踪，只需将该值设置为空即可。
|perfCounterTrace|p|启用性能计数器跟踪的选项。 有效值为“p”或空值。 如果不希望捕获此跟踪，只需将该值设置为空即可。
|networkTrace|n|启用 Netmon 跟踪的选项。 有效值为 **n** 或空值。 如果不希望捕获此跟踪，只需将该值设置为空即可。
|xperfTrace|x|启用 XPerf 跟踪的选项。 有效值为“x”或空值。 如果不希望捕获此跟踪，只需将该值设置为空即可。
|storPortTrace|s|启用 StorPort 跟踪的选项。 有效值为 s 或空值。 如果不希望捕获此跟踪，只需将该值设置为空即可。
|srNumber|123452016365929|支持票证号码（如果有）。 如果不使用，则保留为空。
|requestTimeUtc|9/2/2017 11:06:00 PM|UTC 格式的当前日期时间。 如果使用门户来安装此扩展，则不需要提供此值。
|storageAccountName|mystorageaccount|要存储诊断日志和结果的存储帐户名称。
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|存储帐户的密钥。

## <a name="install-the-extension"></a>安装扩展

请按照这些步骤在 Windows 虚拟机上安装 VM 扩展：

1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 选择你想要安装此扩展的虚拟机。

    ![选择虚拟机](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. 选择“扩展”边栏选项卡，然后单击“添加”按钮。

    ![选择扩展](media/performance-diagnostics-vm-extension/select-extensions.png)
4. 选择 Azure 性能诊断扩展，查看条款和条件，单击“创建”按钮。

    ![创建 Azure 性能诊断扩展](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. 提供用于安装的参数值，然后单击“确定”安装扩展。 有关受支持的故障排除方案的详细信息，可单击[此处](how-to-use-perfInsights.md#supported-troubleshooting-scenarios)进行了解。 

    ![安装扩展](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. 安装成功后，你将会看到一条指示预配成功的消息。

    ![预配成功的消息](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > 预配成功后，扩展执行将随即开始，这需要花费几分钟或更短时间来完成基本方案的执行。 对于其他方案，将在安装过程中指定的持续时间内运行。

## <a name="remove-the-extension"></a>删除扩展
若要从虚拟机中删除该扩展，请按照下列步骤操作：

1. 登录到 [Azure 门户](http://portal.azure.com)，选择你想要在其中删除此扩展的虚拟机，然后选择“扩展”边栏选项卡。 
2. 单击 (...)，从列表中找到性能诊断扩展条目，然后选择“卸载”。

    ![卸载扩展](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > 此外，你也可以选择扩展条目，并选择“卸载”选项。

## <a name="template-deployment"></a>模板部署
可使用 Azure Resource Manager 模板部署 Azure VM 扩展。 可在 Azure 资源管理器模板中使用上一部分中详细说明的 JSON 架构，以在 Azure 资源管理器模板部署期间运行 Azure 性能诊断扩展。 以下是可与模板部署配合使用的示例模板。

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
    "diagnosticsTrace": {
      "type": "string",
      "defaultValue": "d"
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
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell 部署
可以使用 `Set-AzureRmVMExtension` 命令将 Azure 性能诊断虚拟机扩展部署到现有的虚拟机。 运行命令之前，需将公共和专用配置存储在 PowerShell 哈希表中。

PowerShell

````
$PublicSettings = @{ "performanceScenario" = "basic"; "traceDurationInSeconds" = 300; "diagnosticsTrace" = "d"; "perfCounterTrace" = "p"; "networkTrace" = ""; "xperfTrace" = ""; "storPortTrace" = ""; "srNumber" = ""; "requestTimeUtc" = "2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName" = "mystorageaccount" ; "storageAccountKey" = "mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>有关捕获的数据信息
PerfInsights 工具将收集各种日志、配置、诊断数据等，具体要视所选方案的而定。 有关每个方案收集的数据的详细信息，请访问 [PerfInsights 文档](http://aka.ms/perfinsights)。

## <a name="view-and-share-the-results"></a>查看和共享结果

默认情况下，扩展输出将存储在临时驱动器（通常是 D:\log_collection）下的名为 log_collection 的文件夹内。 在此文件夹下，可以查看包含诊断日志和报告（提供发现和建议）的 zip 文件。

在安装过程中，还会将创建的 zip 文件上传到提供的存储帐户，并使用[共享访问签名 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) 共享 30 天。 另外，还会在 log_collection 文件夹中创建一个名为“zipfilename”_saslink.txt 的文本文件。 此文件包含创建的 SAS 链接，用于下载 zip 文件。 拥有此链接的任何人都可以下载 zip 文件。

Microsoft 可使用此 SAS 链接下载诊断数据，以便从事票证支持工作的支持工程师做进一步调查。

若要查看报告，只需提取 zip 文件，然后打开“PerfInsights Report.html”文件即可。

或者，也可以通过选择扩展直接从门户下载 zip 文件。

![查看详细状态](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> 由于在编码和解码操作期间 URL 格式不正确（由特殊字符引起），有时在门户中显示的 SAS 链接可能会无效。 解决办法是，请直接从 VM 的 *_saslink.tx 文件获取链接。

## <a name="troubleshoot-and-support"></a>故障排除和支持
### <a name="troubleshoot"></a>故障排除

- 即使已成功预配扩展，（通知区域中的）扩展部署状态可能仍会显示为“正在进行部署”。

    只要扩展状态指示已成功预配扩展，可放心忽略此问题。
- 安装过程中的某些问题，可使用扩展日志进行故障排除。 扩展执行输出将记录到在以下目录中发现的文件：

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>支持

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。
