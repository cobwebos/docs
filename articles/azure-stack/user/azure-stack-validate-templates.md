---
title: "使用模板验证程序 Azure 堆栈检查模板 |Microsoft 文档"
description: "检查模板以部署到 Azure 堆栈"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 6a77efb3ef4236048ff08b14346175b592493982
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="check-your-templates-for-azure-stack-with-template-validator"></a>与模板验证程序的 Azure 堆栈检查你的模板

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以使用模板验证工具用于检查你的 Azure 资源管理器[模板](azure-stack-arm-templates.md)供 Azure 堆栈。 该模板验证工具是可用作 Azure 堆栈的工具的一部分。 通过使用中所述的步骤下载 Azure 堆栈工具[从 GitHub 下载工具](azure-stack-powershell-download.md)文章。 

若要验证模板，你可以使用以下 PowerShell 模块中的**TemplateValidator**和**CloudCapabilities**文件夹： 

 - AzureRM.CloudCapabilities.psm1 创建云功能 JSON 文件表示的服务和版本，例如 Azure 堆栈云中。
 - AzureRM.TemplateValidator.psm1 使用云功能 JSON 文件来测试 Azure 堆栈中的部署模板。
 
在本文中，你生成云功能文件，然后再运行验证程序工具。

## <a name="build-cloud-capabilities-file"></a>生成云功能文件
在使用模板验证程序之前，运行 AzureRM.CloudCapabilities PowerShell 模块，以便生成 JSON 文件。 如果需要更新你的集成的系统，或添加任何新服务或 VM 扩展你也应再次运行该模块。

1.  请确保已连接到 Azure 堆栈。 可以从 Azure 堆栈开发工具包主机上，执行这些步骤也可以使用[VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)从你的工作站连接。 
2.  导入 AzureRM.CloudCapabilities PowerShell 模块：

    ```PowerShell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ``` 

3.  Get CloudCapabilities cmdlet 用于检索服务版本，并创建云功能 JSON 文件。 如果未指定-OutputPath，创建该文件 AzureCloudCapabilities.Json 是当前目录中。 使用你的实际位置：

    ```PowerShell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```             

## <a name="validate-templates"></a>验证模板
在这些步骤中，你可以通过使用 AzureRM.TemplateValidator PowerShell 模块验证模板。 你可以使用自己的模板，或验证[Azure 堆栈快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)。

1.  导入 AzureRM.TemplateValidator.psm1 PowerShell 模块：
    
    ```PowerShell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2.  运行模板验证程序：

    ```PowerShell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

任何模板验证警告或错误记录到 PowerShell 控制台和源目录中的 HTML 文件。 下面是验证报告的示例：

![示例验证报告](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>parameters

| 参数 | 说明 | 需要 |
| ----- | -----| ----- |
| TemplatePath | 指定的路径以递归方式查找 Azure 资源管理器模板 | 是 | 
| TemplatePattern | 指定要匹配的模板文件的名称。 | 否 |
| CapabilitiesPath | 指定云功能 JSON 文件的路径 | 是 | 
| IncludeComputeCapabilities | 包括的 IaaS 资源，如 VM 大小和 VM 扩展的评估 | 否 |
| IncludeStorageCapabilities | 包括存储资源，如 SKU 类型评估 | 否 |
| 报表 | 指定生成的 HTML 报表名称 | 否 |
| 详细 | 将错误和警告记录到控制台 | 否|


### <a name="examples"></a>示例
此示例将验证所有[Azure 堆栈快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)下载到本地，并且还将验证的 VM 大小和针对 Azure 堆栈开发工具包功能的扩展。

```PowerShell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json.json `
-TemplatePattern MyStandardTemplateName.json`
-IncludeComputeCapabilities`
-Report TemplateReport.html
```


## <a name="next-steps"></a>后续步骤
 - [将模板部署到 Azure 堆栈](azure-stack-arm-templates.md)
 - [为 Azure Stack 开发模板](azure-stack-develop-templates.md)

