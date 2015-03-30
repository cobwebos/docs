<properties 
	pageTitle="Azure 批处理开发库和工具" 
	description="获取开发 Azure 批处理和 Batch Apps 应用程序时所需的库和工具" 
	services="batch" 
	documentationCenter="" 
	authors="dlepow" 
	manager="timlt"/>

# Azure 批处理开发库和工具 
获取这些库和工具，以开发 Azure 批处理和 Batch Apps 应用程序。

## 批处理

+ [适用于 .NET 的批处理客户端库](http://www.nuget.org/packages/Azure.Batch/) (NuGet) - 开发客户端应用程序以使用批处理服务运行作业
+ [批处理管理库](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) (NuGet) - 管理批处理服务帐户
+ [批处理资源管理器](https://code.msdn.microsoft.com/windowsazure/Azure-Batch-Explorer-c1d37768) (MSDN) - 用于浏览、访问和更新批处理帐户中的主要元素（包括作业和任务、任务虚拟机 (TVM) 和池以及 TVM 上的文件）的 GUI 应用程序和示例。

> [AZURE.NOTE] 如果你是批处理的新手或者想要监视或排查批处理活动问题，则批处理资源管理器将是一个很好的工具。因为它是一个代码示例，源代码只会显示如何使用批处理 .NET API 实现功能。请参阅[博客文章](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx)。

## Batch Apps

+ [Batch Apps 云 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/) (NuGet) - 使应用程序可将作业卸载到批处理服务
+ [Batch Apps Visual Studio 扩展](https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a) （visual Studio 库）- 使用 Batch Apps 云 SDK 使 Visual Studio 中的应用程序支持云
+ [Batch Apps 客户端 SDK](http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/) (NuGet) - 与应用程序交互，以便将作业卸载到批处理服务
+ [Batch Apps Python 客户端](https://github.com/Azure/azure-batch-apps-python) (GitHub) - Python 客户端模块，用来与 Batch Apps 服务中设置的应用程序交互
+ [Batch Apps Blender 示例](https://github.com/Azure/azure-batch-apps-blender) (GitHub) - Blender 开放源渲染软件的外接程序，它使用 Batch Apps SDK 和 Python 客户端设置基于云的渲染平台


## 其他资源

+ [代码示例](https://code.msdn.microsoft.com/site/search?f[0].Type=Topic&f[0].Value=Azure%20Batch&f[0].Text=Azure%20Batch) (MSDN)
+ [适用于 .NET 的 Azure 批处理库入门](/documentation/articles/batch-dotnet-get-started/)  

<!--Anchors-->
[批处理]: #batch
[Batch Apps]: #batch-apps
[其他资源]:#additional-resources
