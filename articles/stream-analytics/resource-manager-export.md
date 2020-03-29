---
title: 导出 Azure 流分析作业 Azure 资源管理器模板
description: 本文介绍如何为 Azure 流分析作业导出 Azure 资源管理器模板。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968942"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>导出 Azure 流分析作业 Azure 资源管理器模板

[Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)允许您将基础结构作为代码实现。 该模板是一个 JavaScript 对象表示法 （JSON） 文件，用于定义资源的基础结构和配置。 指定要部署的资源和这些资源的属性。

您可以通过导出 Azure 资源管理器模板来重新部署 Azure 流分析作业。

## <a name="open-a-job-in-vs-code"></a>在 VS 代码中打开作业

必须先在可视化工作室代码中打开现有的流分析作业，然后才能导出模板。 

要将作业导出到本地项目，请在 Azure 门户中的**流分析资源管理器**中找到要导出的作业。 从 **"查询**"页中，选择 **"在视觉工作室中打开**"。 然后选择**视觉工作室代码**。

![在可视化工作室代码中打开流分析作业](./media/resource-manager-export/open-job-vs-code.png)

有关使用可视化工作室代码管理流分析作业的详细信息，请参阅[可视化工作室代码快速入门](quick-create-vs-code.md)。

## <a name="compile-the-script"></a>编译脚本 

下一步是将作业脚本编译为 Azure 资源管理器模板。 在编译脚本之前，请确保作业至少配置了一个输入和一个输出。 如果未配置输入或输出，则需要首先配置输入和输出。

1. 在可视化工作室代码中，导航到作业的*转换.asaql*文件。

   ![变形.asaql 文件在可视化工作室代码](./media/resource-manager-export/transformation-asaql.png)

1. 右键单击 *"转换.asaql"* 文件，然后从菜单中选择**ASA：编译脚本**。

1. 请注意 **，"部署"** 文件夹将显示在流分析作业工作区中。

1. 浏览*作业模板.json*文件，该文件是用于部署的 Azure 资源管理模板。

## <a name="complete-the-parameters-file"></a>完成参数文件

接下来，完成 Azure 资源管理模板参数文件。

1. 打开位于可视化工作室代码中的"部署"工作区的 **"部署"** 文件夹中的*作业模板.parameters.json*文件。

1. 请注意，输入和输出键为空。 将空值替换为输入和输出资源的实际访问键。

1. 保存参数文件。

## <a name="deploy-using-templates"></a>使用模板进行部署

可以使用上一节中生成的 Azure 资源管理器模板部署 Azure 流分析作业。

在 PowerShell 窗口中，运行以下命令。 请务必使用实际资源组名称重新使用*资源组名称*、*模板文件*文件和*模板参数文件*，以及作业工作区**的"部署文件夹"** 中*作业模板.json*和*JobTemplate.parameter.json*文件的完整文件路径。

如果未配置 Azure PowerShell，请按照[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-Az-ps)中的步骤操作。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>后续步骤

* [通过 Visual Studio Code，使用实时输入在本地测试 Azure 流分析作业](visual-studio-code-local-run-live-input.md)

* [使用可视化工作室代码浏览 Azure 流分析作业（预览版）](visual-studio-code-explore-jobs.md)