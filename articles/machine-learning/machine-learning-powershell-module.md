<properties
	pageTitle="机器学习 PowerShell 模块 | Microsoft Azure"
	description="Azure 机器学习 PowerShell 模块可在公共预览模式下使用。可使用 PowerShell 来创建和管理工作区、试验和 Web 服务等。"
	keywords="试验, 线性回归, 机器学习算法, 机器学习教程, 预测模型技术, 数据科学实验"
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="08/05/2016"
	ms.author="garye;haining"/>

# Microsoft Azure 机器学习 PowerShell 模块

Azure 机器学习 PowerShell 模块是一个功能强大的工具，让你可以使用 Windows PowerShell 来管理工作区、试验、数据集、Web 服务等。

如需查看文档并下载该模块以及完整的源代码，请前往：[https://aka.ms/amlps](https://aka.ms/amlps)。

## 机器学习 PowerShell 模块是什么？

机器学习 PowerShell 模块是基于 .NET 的 DLL 模块，让你可以完全通过 Windows PowerShell 来管理 Azure 机器学习工作区、试验、数据集、Web 服务和 Web 服务终结点。你可以连同该模块一起下载完整的源代码，其中包括完全分隔的 [C# API 层](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)。这意味着你可以从自己的 .NET 项目中引用此 DLL，并通过 .NET 代码管理 Azure 机器学习。此外，对于 DLL 依赖的基础 REST API，你可以从最喜欢的客户端直接利用。

## PowerShell 模块具有哪些功能？

以下是一些可以使用此 PowerShell 模块执行的任务。对于以下功能及其他更多功能的相关信息，请查看[完整文档](https://aka.ms/amlps)。

- 使用管理证书设置新的工作区 ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- 导出和导入表示试验图形的 JSON 文件（[Export-AmlExperimentGraph](https://github.com/hning86/azuremlps#export-amlexperimentgraph) 和 [Import-AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph)）
- 运行试验 ([Start-AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- 利用预测性试验创建 Web 服务 ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- 在已发布的 Web 服务上创建新的终结点 ([Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- 调用 RRS 和/或 BES Web 服务终结点（[Invoke-AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) 和 [Invoke-AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)）

下面是使用 PowerShell 运行现有试验的一个简单示例︰

		#Find the first Experiment named “xyz”
		$exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
		#Run the Experiment
		Start-AmlExperiment -ExperimentId $exp.ExperimentId 

若要深入了解使用案例，请参阅此文章了解如何使用 PowerShell 模块自动执行非常普遍请求的任务︰[使用 PowerShell 从一个试验中创建许多机器学习模型和 Web 服务终结点](machine-learning-create-models-and-endpoints-with-powershell.md)。

## 如何开始？

若要开始使用机器学习 PowerShell，请从 GitHub 下载“[发行包](https://github.com/hning86/azuremlps/releases)”并按照“[安装说明](https://github.com/hning86/azuremlps/blob/master/README.md)”执行操作。你将需要取消阻止已下载/解压缩的 DLL，然后将它导入到 PowerShell 环境。大多数 cmdlet 都要求你提供工作区 ID、工作区授权令牌和工作区所在的 Azure 区域。提供这些信息的最简单方法是使用默认 config.json 文件，安装说明中对其进行了详细解说。当然，你也可以使用 Visual Studio 克隆 git 树并在本地修改/编译代码。

## 后续步骤

此预览期间，我们会持续改进和扩展 PowerShell 模块。如需更多新闻和信息，敬请关注 [Cortana Intelligence 和机器学习博客](https://blogs.technet.microsoft.com/machinelearning/)。

<!---HONumber=AcomDC_0921_2016-->