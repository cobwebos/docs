<properties title="管理 Azure 网站" pageTitle="管理 Azure 网站" description="用于管理 Microsoft Azure 网站的资源链接." services="web-sites" solutions="web" documentationCenter="" authors="mwasson" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="mwasson" />

# 管理 Azure 网站

本主题包含用于管理 Microsoft Azure 网站的资源链接。管理包括维持网站平稳运行的所有任务。 

在整个站点使用期内，您将执行各种管理任务，从初始部署到正常操作、维护与更新。

- [将站点部署为生产环境之前]
- [网站运行期间]
- [更新网站时]

许多网站管理任务都可在 Azure 门户中执行。更多详细信息，请参阅[通过 Azure 管理门户管理网站](/zh-cn/documentation/articles/web-sites-manage/)。

## 将站点部署为生产环境之前

### 选择层级

Azure 网站可提供四个层：免费、共享、基本和标准。有关各层的特性与定价的信息，请参阅[定价详情](/zh-cn/pricing/details/websites/)。 

- [Web 托管计划](/zh-cn/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview)支持您在同一层对多个网站进行分组。
- 您可以在创建网站之后经常[切换层](/zh-cn/documentation/articles/web-sites-scale/)。

### 配置

使用[Azure 管理门户](https://manage.windowsazure.cn/)设置各种配置选项。详情请参阅[如何配置网站](/zh-cn/documentation/articles/web-sites-configure/)。下面是快速核对清单：

- 如有需要，请选择针对 .NET、PHP、Java 或 Python 的**运行时版本**。
- 如果您的网站使用 WebSocket 协议，请启用**WebSocket**。（这包括使用[ASP.NET SignalR](http://www.asp.net/signalr)或[socket.io](/zh-cn/documentation/articles/web-sites-nodejs-chat-app-socketio/)的应用程序。）
- 您是否要运行持续 web 作业？如果是，请启用**Always On**。
- 设置**默认文档**，例如 index.html。

除了这些基本配置设置，您可能还需要进行下列配置：

- **安全套接字层 (SSL)**加密。若要使用带有自定义域名的 SSL，您必须获取 SSL 证书并配置网站。请参阅[为 Azure 网站启用 HTTPS](/zh-cn/documentation/articles/web-sites-configure-ssl-certificate/)。
- **自定义域名。**您的网站在 azurewebsites.net 下自动带有子域。可以关联自定义域名，例如 contoso.com。请参阅[配置自定义域名](/zh-cn/documentation/articles/web-sites-custom-domain-name/)。

特定于语言的配置：

- **PHP**：[如何在 Azure 网站中配置 PHP](/zh-cn/documentation/articles/web-sites-php-configure/)。
- **Python**：[配置 Azure 网站的 Python](/zh-cn/documentation/articles/web-sites-python-configure/)


## 网站运行期间

在站点运行期间，如果您要确保其可用性，并能够进行缩放以满足用户流量。您可能还需要解决错误。

### 监视

- 通过管理门户，您可以[添加性能度量值](/zh-cn/documentation/articles/web-sites-monitor)（如 CPU 使用率和客户端请求数）。
- 如欲获取更加深入的洞察，请使用 New Relic 监视和管理性能。请参阅[Azure 网站上的 New Relic 应用程序性能管理](/zh-cn/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/)。
- [缩放您的网站](/zh-cn/documentation/articles/web-sites-scale/)以响应流量。您可以根据不同的层缩放虚拟机数量和/或虚拟机实例的大小。在标准计划中，您还可以设置自动缩放，那么您的网站将能够根据固定计划，或以负载为依据进行自动缩放。  
 
### 备份

- 设置网站的[自动备份](/zh-cn/documentation/articles/web-sites-backup/)。观看[本视频](/zh-cn/documentation/videos/azure-websites-automatic-and-easy-backup/)了解更多关于备份的信息。
- 了解 Azure SQL Database 的[数据库恢复](http://msdn.microsoft.com/library/azure/hh852669.aspx)选项。

### 故障排除

- 如果出现问题，您可以使用云中的诊断日志和实时调试[在 Visual Studio 中排除故障](/zh-cn/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/#remotedebug)。 
- 在 Visual Studio 之外还提供了不同的诊断日志收集方法。请参阅[启用 Azure 网站的诊断日志记录](/zh-cn/documentation/articles/web-sites-enable-diagnostic-log/)。
- 关于 Node.js 应用程序，请参阅[如何在 Azure 网站中调试 Node.js 应用程序](/zh-cn/documentation/articles/web-sites-nodejs-debug/)。

### 还原数据

- [还原](zh-cn/documentation/articles/web-sites-restore/)之前备份的网站。


## 更新网站时

如果尚未启用自动备份，您可以创建[手动备份](/zh-cn/documentation/articles/web-sites-backup/)。

请考虑使用[分阶段部署](/zh-cn/documentation/articles/web-sites-staged-publishing/)。该选项可支持您向与生产部署并排运行的分阶段部署发布更新。 

如果使用 Visual Studio Online，您可以通过源控件设置持续部署：

- [使用 Team Foundation 版本控件 (TFVC)](/zh-cn/documentation/articles/cloud-services-continuous-delivery-use-vso/) 
- [使用 Git](/zh-cn/documentation/articles/cloud-services-continuous-delivery-use-vso-git/)
 

 
<!-- Anchors. -->


[将站点部署为生产环境之前]: #before-you-deploy-your-site-to-production
[网站运行期间]: #while-your-website-is-running
[更新网站时]: #when-you-update-your-website

 
