---
title: 使用模板验证工具检查 Azure 堆栈模板 |Microsoft 文档
description: 检查要部署到 Azure Stack 的模板
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 88fac41ce2c9fa0c5569beae02ab90a507c89a34
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="check-your-templates-for-azure-stack-with-the-template-validation-tool"></a>使用模板验证工具 Azure 堆栈检查你的模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以使用模板验证工具用于检查你的 Azure 资源管理器[模板](azure-stack-arm-templates.md)准备好部署到 Azure 堆栈。 模板验证工具是 Azure Stack 工具的一部分。 使用[从 GitHub 下载工具](azure-stack-powershell-download.md)一文中所述的步骤下载 Azure Stack 工具。

## <a name="overview"></a>概述

若要验证模板，你必须构建云的功能，文件第一个，然后运行验证工具。 使用以下 PowerShell 模块从 Azure 堆栈工具：

- 在**TemplateValidator**文件夹：<br>         AzureRM.CloudCapabilities.psm1 创建云功能 JSON 文件表示的服务和 Azure 堆栈云中的版本。
- 在**CloudCapabilities**文件夹：<br>
AzureRM.TemplateValidator.psm1 使用云功能 JSON 文件来测试要在 Azure Stack 中部署的模板。

## <a name="build-the-cloud-capabilities-file"></a>生成云功能文件

使用模板验证程序之前，先运行 AzureRM.CloudCapabilities PowerShell 模块以生成 JSON 文件。

>[!NOTE]
>如果你更新你的集成的系统，或添加任何新服务或虚拟扩展时，应再次运行此模块。

1. 请确保已连接到 Azure Stack。 这些步骤可从 Azure Stack 开发工具包主机执行，也可以使用 [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 从工作站连接。
2. 导入 AzureRM.CloudCapabilities PowerShell 模块：

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. 使用 Get-CloudCapabilities cmdlet 检索服务版本，并创建云功能 JSON 文件。 如果没有指定 **-OutputPath**，AzureCloudCapabilities.Json 创建当前目录中的文件。 使用实际位置：

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>验证模板

使用以下步骤来验证通过使用 AzureRM.TemplateValidator PowerShell 模块的模板。 可以使用自己的模板，或验证 [Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)。

1. 导入 AzureRM.TemplateValidator.psm1 PowerShell 模块：

    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. 运行模板验证程序：

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

模板验证警告或错误记录到 PowerShell 控制台和源目录中的 HTML 文件。 以下屏幕截图显示了一份验证报告的示例：

![模板验证报告](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parameters

模板验证程序支持以下参数。

| 参数 | 说明 | 需要 |
| ----- | -----| ----- |
| TemplatePath | 指定要在其中递归查找 Azure 资源管理器模板的路径 | 是 | 
| TemplatePattern | 指定要匹配的模板文件的名称。 | 否 |
| CapabilitiesPath | 指定云功能 JSON 文件的路径 | 是 | 
| IncludeComputeCapabilities | 包括 IaaS 资源（如 VM 大小和 VM 扩展）的评估 | 否 |
| IncludeStorageCapabilities | 包括存储资源（如 SKU 类型）的评估 | 否 |
| 报表 | 指定生成的 HTML 报告的名称 | 否 |
| 详细 | 将错误和警告记录到控制台 | 否|

### <a name="examples"></a>示例

此示例将验证的所有[Azure 堆栈快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)下载到本地存储区。 该示例还将验证虚拟机大小和针对 Azure 堆栈开发工具包功能的扩展。

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```

## <a name="next-steps"></a>后续步骤

- [将模板部署到 Azure Stack](azure-stack-arm-templates.md)
- [为 Azure Stack 开发模板](azure-stack-develop-templates.md)
