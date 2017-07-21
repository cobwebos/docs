---
title: "管理 Azure 应用服务中的 Web 应用"
description: "用于管理 Azure 应用服务中 Web 应用的资源链接。"
services: app-service\web
documentationcenter: 
author: erikre
manager: erikre
editor: 
ms.assetid: d5e2887a-84f9-4747-a573-867635cb8b39
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: rachelap
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 9e19618a1b24bbdf3163ddfc3423c5c932dcd7af
ms.contentlocale: zh-cn
ms.lasthandoff: 07/19/2017

---
# <a name="manage-a-web-app-in-azure-app-service"></a>管理 Azure 应用服务中的 Web 应用
本主题包含用于管理 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)中 Web 应用的资源链接。 管理包括维持 Web 应用平稳运行的所有任务。 

在整个 Web 应用使用期内，您将执行各种管理任务，从初始部署到正常操作、维护与更新。

许多 Web 应用管理任务都可在 Azure 门户中执行。

## <a name="before-you-deploy-your-web-app-to-production"></a>将 Web 应用部署到生产之前
### <a name="choose-a-tier"></a>选择层
Azure 应用服务在五个层中提供：免费、共享、基本、标准和高级。 有关各层的功能与定价的信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/app-service/)。 

* [应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)支持将多个 Web 应用分组到同一层中。
* 在创建 Web 应用后可以随时[切换层](web-sites-scale.md)。

### <a name="configuration"></a>配置
使用 [Azure 门户](https://portal.azure.com/)设置各种配置选项。 有关详细信息，请参阅[在 Azure 应用服务中配置 Web 应用](web-sites-configure.md)。 下面是快速核对清单：

* 选择用于 .NET、PHP、Java 或 Python 的**运行时版本**（如果需要）。
* 如果 Web 应用使用 WebSocket 协议，请启用 **WebSocket**。 （这包括使用 [ASP.NET SignalR](http://www.asp.net/signalr) 或 [socket.io](web-sites-nodejs-chat-app-socketio.md) 的应用。）
* 是否要运行持续 web 作业？ 如果是，请启用**始终打开**。
* 设置**默认文档**，例如 index.html。

除了这些基本配置设置，可能还需要进行下列配置：

* **安全套接字层 (SSL)** 加密。 若要使用带有自定义域名的 SSL，必须获取 SSL 证书并配置 Web 应用。 请参阅[为 Azure 应用服务中的 Web 应用启用 HTTPS](app-service-web-tutorial-custom-ssl.md)。
* **自定义域名。** Web 应用在 azurewebsites.net 下自动具有一个子域。 可以关联自定义域名（如 contoso.com ）。 请参阅[在 Azure 应用服务中配置自定义域名](app-service-web-tutorial-custom-domain.md)。

特定于语言的配置：

* **PHP**：[在 Azure 应用服务 Web 应用中配置 PHP](web-sites-php-configure.md)。
* **Python**：[使用 Azure 应用服务 Web 应用配置 Python](web-sites-python-configure.md)

## <a name="while-your-web-app-is-running"></a>Web 应用运行期间
在 Web 应用运行期间，需要确保其可用性，并能够进行缩放以满足用户流量。 可能还需要解决错误。

### <a name="monitoring"></a>监视
* 通过 Azure 门户，可以[添加性能度量值](web-sites-monitor.md)（如 CPU 使用率和客户端请求数）。
* [缩放 Web 应用](web-sites-scale.md)以响应流量。 可以根据不同的层缩放虚拟机数量和/或虚拟机实例的大小。 在标准层和高级层中，还可以设置自动缩放，因此，Web 应用将能够根据固定计划，或根据负载进行自动缩放。  

### <a name="backups"></a>备份
* 设置 Web 应用的[自动备份](web-sites-backup.md)。 观看[此视频](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/)了解关于备份的更多信息。
* 了解 Azure SQL 数据库的[数据库恢复](../sql-database/sql-database-business-continuity.md)选项。

### <a name="troubleshooting"></a>故障排除
* 如果出现问题，可以使用云中的诊断日志和实时调试[在 Visual Studio 中排除故障](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)。 
* 在 Visual Studio 之外还提供了不同的诊断日志收集方法。 请参阅[在 Azure 应用服务中为 Web 应用启用诊断日志记录](web-sites-enable-diagnostic-log.md)。
* 关于 Node.js 应用程序，请参阅[如何调试 Azure 应用服务中的 Node.js Web 应用](web-sites-nodejs-debug.md)。

### <a name="restoring-data"></a>还原数据
* [还原](web-sites-restore.md)之前已备份的 Web 应用。

## <a name="when-you-update-your-web-app"></a>更新 Web 应用时
如果尚未启用自动备份，可以创建[手动备份](web-sites-backup.md)。

请考虑使用[过渡部署](web-sites-staged-publishing.md)。 此选项允许向与生产部署并行运行的过渡部署发布更新。 


<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website



