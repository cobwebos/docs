---
title: "Azure Web 作业文档资源"
description: "学习如何使用 Azure Web 作业和 Azure WebJobs SDK 时可以参考的推荐资源"
services: app-service
documentationcenter: .net
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: ed005e56-4334-4641-a5e5-15435c2be36b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 5050b442540632086eda80bd7fecaf2beb8ddb50
ms.contentlocale: zh-cn
ms.lasthandoff: 09/15/2017

---
# <a name="azure-webjobs-documentation-resources"></a>Azure Web 作业文档资源
## <a name="overview"></a>概述
本主题提供了有关如何使用 Azure Web 作业和 Azure WebJobs SDK 的文档资源的链接。 通过使用 Azure Web 作业，可以方便地在[应用服务 Web 应用、API 应用或移动应用](../app-service/app-service-value-prop-what-is.md)的上下文中将脚本或程序作为后台进程运行。 可上传和运行可执行文件，例如 cmd、bat、exe (.NET)、ps1、sh、php、py、js 和 jar。 这些程序将按计划 (cron) 或者连续地以 Web 作业的形式运行。

[WebJobs SDK](https://docs.microsoft.com/azure/app-service-web/websites-dotnet-webjobs-sdk) 用于简化针对 Web 作业执行的常见任务（如图像处理、队列处理、RSS 聚合、文件维护和发送电子邮件）编写的代码。 WebJobs SDK 的内置功能使用 Azure 存储和 Service Bus，用于计划任务和处理错误，以及用于许多其他常见方案。 此外，还可以扩展其设计，且拥有[用于扩展的开源存储库](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。 [Azure Functions](../azure-functions/functions-overview.md)（当前以预览版提供）基于使用 C# 脚本、Node.js 和其他语言的 WebJobs SDK 的版本。 

[!INCLUDE [app-service-web-webjobs-corenote](../../includes/app-service-web-webjobs-corenote.md)]

使用 Visual Studio 中集成的工具，可以顺利地创建、部署和管理 Web 作业。 可从模板创建 Web 作业，还可以发布和管理（运行、停止、监视和调试）这些作业。 

Azure 门户中的 WebJobs 仪表板提供了强大的管理功能，可让你全面控制 WebJobs 的执行，包括调用 WebJobs 中的各个函数。 该仪表板还会显示函数运行时和日志记录输出。 

## <a name="getstarted"></a>WebJobs 和 WebJobs SDK 入门
* [Azure Web 作业简介](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure Web 作业功能很强大，快来试试吧！](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) （Troy Hunt 的博客文章。）
* [Azure Web 作业功能](https://azure.microsoft.com/blog/2014/10/22/webjobs-goes-into-full-production/)
* [什么是 WebJobs SDK](websites-dotnet-webjobs-sdk.md)
* [Microsoft 模式与实践中的后台作业指南](https://docs.microsoft.com/azure/architecture/best-practices/background-jobs)
* [宣布推出 Microsoft Azure WebJobs SDK 1.1.0 RTM](https://azure.microsoft.com/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Azure WebJobs SDK 入门](websites-dotnet-webjobs-sdk-get-started.md)
* [如何通过 WebJobs SDK 使用 Azure 队列存储](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [如何通过 WebJobs SDK 使用 Azure Blob 存储](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [如何通过 WebJobs SDK 使用 Azure 表存储](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [如何通过 WebJobs SDK 使用 Azure 服务总线](websites-dotnet-webjobs-sdk-service-bus.md)
* [Azure WebJobs SDK 快速参考（PDF 下载）](https://go.microsoft.com/fwlink/p/?linkid=845558)
* [GitHub 中的 WebJobs 设置文档](https://github.com/projectkudu/kudu/wiki/Web-jobs)。
* 视频
  * [WebJobs 和 WebJobs SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
  * [第 9 频道上的 Azure WebJobs 视频系列](http://channel9.msdn.com/Tags/azurefridaywebjobs)
  * [WebJobs Tooling for Visual Studio 简介](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [WebJobs Tooling 和远程调试](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
  * [Pranav Rastogi 提供的 Azure Web 作业更新 - 1.1 版中的可扩展性](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

另请参阅以下有关[部署 Web 作业](#deploy)及[测试和调试 Web 作业](#debug)的部分。

## <a name="deploy"></a>部署 WebJobs
* [如何使用 Visual Studio 部署 Azure 作业](websites-dotnet-deploy-webjobs.md)
* [如何使用 Azure 门户部署 Web 作业](web-sites-create-web-jobs.md)
* [启用 Azure Web 作业的命令行或连续传送](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [使用 Web 作业通过 Git 将 .NET 控制台应用程序部署到 Azure](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [将 F# Web 作业部署到 Azure](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [将自定义服务部署为 Azure Web 作业](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* 视频
  * [WebJobs Tooling for Visual Studio 简介](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
  * [WebJobs Tooling 和远程调试](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="schedule"></a>计划 WebJobs
* [添加 Azure Web 作业对话框](websites-dotnet-deploy-webjobs.md#configure)
* [在 Azure 门户中创建计划的 Web 作业](web-sites-create-web-jobs.md#CreateScheduledCRON)
* [将计划程序作业挂接到 Web 作业](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [通过 cron 表达式计划 Azure Web 作业](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [使用 WebJobs SDK TimerTrigger 计划单个 Web 作业函数](websites-dotnet-webjobs-sdk.md#schedule)

## <a name="debug"></a>测试和调试 WebJobs
* [Visual Studio 中 Azure Web 作业的新开发人员和调试功能](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [查看 Web 作业仪表板](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [如何使用 WebJobs SDK 写入日志并在仪表板中查看日志](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [远程调试 WebJobs](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [谁写入了 Blob？](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [在云中托管交互式代码](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [在 Azure Web 作业中添加跟踪](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [监视、诊断和排查 Microsoft Azure 存储问题](../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
* 视频
  * [WebJobs Tooling 和远程调试](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

## <a name="scale"></a>缩放 WebJobs
* [缩放 Azure 网站上的 Web 应用程序](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure 应用服务：构建随时可投入业务的大规模 Web 应用](https://channel9.msdn.com/Events/Build/2014/3-626)。 介绍如何使用 Web 作业（包括 WebJobs SDK）缩放 Web 应用。
* 视频
  * [向外缩放 WebJobs](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

## <a name="additional"></a>其他 WebJobs 资源
* [Magnus Mårtensson 发表的 Azure Web 作业 GA 博客文章](http://magnusmartensson.com/azure-webjobs-ga)
* [在 Azure 应用服务上运行 Powershell Web 作业](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [在 Azure 完成触发 Web 作业时接收通知](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [使用 Web 作业创建简单的 Web 应用备份保留策略](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [在处理第一个请求时 Azure Web 应用和云服务速度缓慢](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/)。 演示如何使用 Web 作业来模拟仅适用于标准定价层的 AlwaysOn 功能。
* [Web 作业正常关闭](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl)。 对于 WebJobs SDK 正常关闭，请参阅[正常关闭](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful)。）
* [使用 Azure Web 作业和 AzCopy 自动备份](http://markjbrown.com/azure-webjobs-azcopy/)
* 视频
  * [Magnus Mårtensson 主讲的 Azure WebJobs 视频](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
  * [第 9 频道上的 Azure WebJobs 视频系列](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="additionalsdk"></a>其他 WebJobs SDK 资源
* [WebJobs SDK 发行说明](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [WebJobs SDK 源代码](https://github.com/Azure/azure-webjobs-sdk)
* [WebJobs SDK 扩展源代码](https://github.com/Azure/azure-webjobs-sdk-extensions)，其中包含[扩展性模型的详细指南](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。  
* [WebJobs SDK 脚本源代码](https://github.com/Azure/azure-webjobs-sdk-script/)（用于 [Azure Functions](../azure-functions/functions-overview.md)）
* [使用 WebJobs SDK 将 FREB 文件上传到 Azure 存储的 Web 作业](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [在 Azure 外部托管 Web 作业，获得 Azure 托管 Web 作业的日志记录优势](http://bypassion.dk/?p=510)
* [使用 Azure Web 作业生成数据导入工具](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Azure Functions 概述](../azure-functions/functions-overview.md)
* 视频
  * [第 9 频道上的 Azure WebJobs 视频系列](http://channel9.msdn.com/Tags/azurefridaywebjobs)

## <a name="samples"></a>示例 WebJob 应用程序
* [Web 作业团队在 GitHub 上提供的示例应用程序](https://github.com/azure/azure-webjobs-sdk-samples)
* [使用 WebJobs SDK 创建包含 Web 作业后端的简单 Azure Web 应用](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77)。 演示计划 Web 作业和事件驱动型 Web 作业的用法。 请参阅博客文章[使用 Azure WebJobs SDK 重新生成 SiteMonitR](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs)。

## <a name="blogs"></a>博客
* [Azure 博客](/blog)
* [Amit Apple 的博客](http://blog.amitapple.com/)。 重点探讨 Web 作业（而不是 SDK）。
* [Magnus Mårtensson 的博客](http://magnusmartensson.com/)

## <a name="gethelp"></a>获取有关 WebJobs 的帮助
* [Web 作业堆栈溢出](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [WebJobs SDK 堆栈溢出](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [Azure Functions 堆栈溢出](http://stackoverflow.com/questions/tagged/azure-functions)
* [Azure 和 ASP.NET 论坛](http://forums.asp.net/1247.aspx)
* [Azure 应用服务 Web 应用论坛](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Azure Web 应用用户之声站点](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/)。 使用井号标签 #AzureWebJobs。
* [报告 Web 作业错误或问题](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)


