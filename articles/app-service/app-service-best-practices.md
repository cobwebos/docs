---
title: "有关 Azure 应用服务的最佳实践"
description: "了解有关 Azure 应用服务的最佳实践和故障排除步骤。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 251ce238b745734bdfb508b30097304a9a650a8c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2017
---
# <a name="best-practices-for-azure-app-service"></a>有关 Azure 应用服务的最佳实践
本文汇总了有关使用 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)的最佳实践。 

## <a name="colocation"></a>共置
如果将编写解决方案（例如 Web 应用和数据库）的 Azure 资源定位在不同的区域，则影响可能包括：

* 增大资源之间通信的延迟
* [Azure 定价页](https://azure.microsoft.com/pricing/details/data-transfers)中列出了跨区域出站数据传输的收费

相同区域中的共置最适合用于组成解决方案的 Azure 资源（例如 Web 应用），以及用于保存内容或数据的数据库或存储帐户。 创建资源时，应确保它们位于同一个 Azure 区域，除非有具体的业务或设计理由需要将它们放在不同的区域。 可以利用高级应用服务计划应用当前可用的[应用服务克隆功能](app-service-web-app-cloning.md)，将应用服务应用移至数据库所在的区域。   

## <a name="memoryresources"></a>当应用占用的内存比预期更多时
如果通过监视或者参考服务建议，发现应用消耗的内存超出指定的预期值，请考虑使用[应用服务自动修复功能](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)。 自动修复功能的选项之一是根据内存阈值采取自定义操作。 这些操作的范围包括发出电子邮件通知、通过内存转储提供调查依据，以及通过回收工作进程在现场消除问题。 可以根据这篇有关[应用服务支持站点扩展](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)的博文中所述，通过 web.config 或者友好的用户界面来配置自动修复。   

## <a name="CPUresources"></a>当应用占用的 CPU 比预期更多时
如果通过监视或者参考服务建议，发现应用消耗的 CPU 超出预期，或者反复出现 CPU 高峰，请考虑向上缩放或向外缩放应用服务计划。 如果应用程序是有状态的，则向上缩放是唯一选项；如果应用程序是无状态的，则向外缩放可以提供更高的灵活性和更大的缩放潜力。 

有关“有状态”与“无状态”应用程序的详细信息，请观看此视频：[Planning a Scalable End-to-End Multi-Tier Application on Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid)（在 Microsoft zure Web 应用中规划可缩放的端到端多层应用程序）。 有关应用服务缩放和自动缩放选项的详细信息，请阅读：[Scale a Web App in Azure App Service](web-sites-scale.md)（在 Azure 应用服务中缩放 Web 应用）。  

## <a name="socketresources"></a>当套接字资源耗尽时
耗尽出站 TCP 连接的一个常见原因是使用的客户端库未实施为重复使用 TCP 连接，或者使用了较高级别的协议（如 HTTP），因而未利用 Keep-Alive。 请查看应用服务计划中的应用引用的每个库，以确保在代码中配置或访问这些库时，能够有效地重复使用出站连接。 此外，请遵循有关正确执行创建和发布或清理操作的库指导文档，以避免连接泄漏。 在展开此类客户端库调查的过程中，可以通过向外扩展到多个实例来消除影响。

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js 和传出 http 请求
使用 Node.js 和许多传出 http 请求时，处理 HTTP（保持活动状态）确实很重要。 可以使用 [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` 包更容易地在代码中实现此功能。

应始终处理 `http` 响应，即使在处理程序中不执行任何操作，也要如此。 如果未正确地处理响应，由于没有更多套接字可用，最终应用程序会停止响应。

例如，使用 `http` 或 `https` 包时：

```
var request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

如果在具有多个核心的计算机中的 Linux 应用服务 上运行，另一种最佳做法是使用 PM2 启动多个 Node.js 进程执行应用程序。 可以通过指定容器的启动命令来执行此操作。

例如，若要启动四个实例：

`pm2 start /home/site/wwwroot/app.js --no-daemon -i 4`

## <a name="appbackup"></a>当应用备份开始失败时
应用备份失败的两个最常见原因：存储设置无效和数据库配置无效。 这些失败通常发生在对存储或数据库资源或其访问方式进行了更改（例如更新了备份设置中所选数据库的凭据）时。 备份通常按计划运行并且只需访问存储（以便输出备份后的文件）和数据库（以便复制和读取备份中要包含的内容）。 其中任一资源访问失败将导致持续备份失败。 

出现备份失败时，请查看最新结果以了解所出现失败的类型。 如果存储访问失败，请查看并更新备份配置中使用的存储设置。 如果数据库访问失败，请查看并更新应用设置中的连接字符串，然后继续将备份配置更新为正确地包括所需数据库。 有关应用备份的详细信息，请参阅 [Back up a web app in Azure App Service](web-sites-backup.md)（在 Azure 应用服务中备份 Web 应用）文档。

## <a name="nodejs"></a>将新的 Node.js 应用部署到 Azure 应用服务时
适用于 Node.js 应用的 Azure 应用服务默认配置旨在符合最常见应用的需求。 如果 Node.js 应用的配置可从个性化调整中受益，使性能提升或 CPU/内存/网络资源的资源使用情况得到优化，用户可以查看我们的最佳实践和故障排除步骤。 这篇文章介绍了可能需要为 Node.js 应用配置的 iisnode 设置、应用可能面临的各种案例或问题，并说明了如何解决这些问题：[Best practices and troubleshooting guide for Node applications on Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)（有关 Azure 应用服务上 Node 应用程序的最佳实践和故障排除指南）。   

