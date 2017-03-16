---
title: "机器学习 PowerShell 模块 | Microsoft Docs"
description: "Azure 机器学习 PowerShell 模块可在公共预览模式下使用。 使用 PowerShell 创建和管理工作区、实验和 Web 服务等。"
keywords: "试验, 线性回归, 机器学习算法, 机器学习教程, 预测建模技术, 数据科研试验"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: a9001cc2-3aa0-47e1-b175-1f76408ba1d1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2016
ms.author: garye;haining
translationtype: Human Translation
ms.sourcegitcommit: 247d370c1f80729856e53690045991127ad54351
ms.openlocfilehash: ee3255493760917b2a96facfabe17120764cb638
ms.lasthandoff: 03/02/2017


---
# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Microsoft Azure 机器学习 PowerShell 模块
Azure 机器学习的 PowerShell 模块是一个功能强大的工具，使你可以使用 Windows PowerShell 来管理工作区、试验、数据集、经典 Web 服务等。

如需查看文档并下载该模块以及完整的源代码，请前往： [https://aka.ms/amlps](https://aka.ms/amlps)。 

> [!NOTE]
> Azure 机器学习 PowerShell 模块当前处于预览模式。 此预览期间，我们会持续改进和扩展该模块。 如需新闻和信息，敬请关注 [Cortana Intelligence 和机器学习博客](https://blogs.technet.microsoft.com/machinelearning/) 。

## <a name="what-is-the-machine-learning-powershell-module"></a>机器学习 PowerShell 模块是什么？
机器学习 PowerShell 模块是基于 .NET 的 DLL 模块，使你可以通过 Windows PowerShell 来完全管理 Azure 机器学习工作区、试验、数据集、经典 Web 服务和经典 Web 服务终结点。 

可以连同该模块一起下载完整的源代码，其中包括完全分隔的 [C# API 层](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)。 可以从自己的 .NET 项目中引用此 DLL，并通过 .NET 代码管理 Azure 机器学习。 此外，DLL 依赖于可以从最喜欢的客户端直接使用的基础 REST API。

## <a name="what-can-i-do-with-the-powershell-module"></a>PowerShell 模块具有哪些功能？
以下是一些可以使用此 PowerShell 模块执行的任务。 对于以下功能及其他更多功能的相关信息，请查看 [完整文档](https://aka.ms/amlps) 。

* 使用管理证书设置新的工作区 ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
* 导出和导入表示实验图形的 JSON 文件（[Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) 和 [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph)）
* 运行试验 ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
* 利用预测性试验创建 Web 服务 ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
* 在已发布的 Web 服务上创建终结点 ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
* 调用 RRS 和/或 BES Web 服务终结点（[Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) 和 [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)）

下面是使用 PowerShell 运行现有试验的一个简单示例︰

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

若要深入了解使用案例，请参阅此文章了解如何使用 PowerShell 模块自动执行普遍请求的任务：[使用 PowerShell 从一个试验中创建许多机器学习模型和 Web 服务终结点](machine-learning-create-models-and-endpoints-with-powershell.md)。

## <a name="how-do-i-get-started"></a>如何入门？
若要开始使用机器学习 PowerShell，请从 GitHub 下载[发行包](https://github.com/hning86/azuremlps/releases)并按照[安装说明](https://github.com/hning86/azuremlps/blob/master/README.md)执行操作。 这些说明解释了如何取消阻止已下载/解压缩的 DLL，然后将它导入到 PowerShell 环境。 大多数 cmdlet 都要求你提供工作区 ID、工作区授权令牌和工作区所在的 Azure 区域。 提供值最简单的方法是通过默认的 config.json 文件。 这些说明也解释了如何配置此文件。 

如果你需要，则可以克隆 git 树，修改代码，并使用 Visual Studio 在本地编译它。

## <a name="next-steps"></a>后续步骤
你可以 [https://aka.ms/amlps](https://aka.ms/amlps) 处找到 PowerShell 模块的完整文档。 

有关如何在实际方案中使用该模块的扩展示例，请查看深入用例[使用 PowerShell 从一个实验中创建多个机器学习模型和 Web 服务终结点](machine-learning-create-models-and-endpoints-with-powershell.md)。

