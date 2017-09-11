---
title: "Azure 上的企业级 WordPress | Microsoft Docs"
description: "了解如何在 Azure 应用服务上托管企业级 WordPress 网站"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 22d68588-2511-4600-8527-c518fede8978
ms.service: app-service-web
ms.devlang: php
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 21281955458a2632d96a91d884cab13803f4d296
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="enterprise-class-wordpress-on-azure"></a>Azure 上的企业级 WordPress
Azure 应用服务为大规模任务关键型 [WordPress][wordpress] 站点提供了一个可缩放、安全且易用的环境。 Microsoft 自身在运营 [Office][officeblog] 和[必应][bingblog]博客等企业级站点。 本文说明如何使用 Microsoft Azure 应用服务的 Web 应用功能，建立和维护一个可处理大量访客、基于云的企业级 WordPress 站点。

## <a name="architecture-and-planning"></a>架构与规划
基本的 WordPress 安装只有两个要求：

* **MySQL 数据库**：此要求通过 [Azure 应用商店中的 ClearDB][cdbnstore] 提供。 作为替代方法，可以使用 [Windows][mysqlwindows] 或 [Linux][mysqllinux] 在 Azure 虚拟机上管理自己的 MySQL 安装。

  > [!NOTE]
  > ClearDB 提供了几种 MySQL 配置。 每种配置具有不同的性能特征。 请参阅 [Azure 应用商店][cdbnstore]，了解通过 Azure 应用商店提供或在 [ClearDB 网站](http://www.cleardb.com/pricing.view)上直接显示的产品的相关信息。
  >
  >
* **PHP 5.2.4 或更高版本**：Azure 应用服务目前提供 [PHP 5.4、5.5 和 5.6 版本][phpwebsite]。

  > [!NOTE]
  > 我们建议始终运行最新版本的 PHP，以确保拥有最新的安全修补程序。
  >
  >

### <a name="basic-deployment"></a>基本部署
如果只使用基本要求，可在 Azure 区域内创建一个基本解决方案。

![在单个 Azure 区域中托管的 Azure Web 应用和 MySQL 数据库][basic-diagram]

尽管这允许创建多个站点 Web 应用实例以扩展应用程序，但所有内容都托管在特定地理区域的数据中心内。 如果此区域外的访客使用此站点，响应时间可能较长。 如果此区域的数据中心停机，那么应用程序也会停机。

### <a name="multi-region-deployment"></a>多区域部署
通过使用 Azure [流量管理器][trafficmanager]，可在多个地理地区扩展 WordPress 站点，并为所有访客提供同一 URL。 所有访客都通过流量管理器接入，并基于负载均衡配置被路由到某一区域。

![一个托管在多个区域的 Azure Web 应用，使用 CDBR 高可用性路由器跨区域路由到 MySQL][multi-region-diagram]

在每个区域中，WordPress 站点仍会扩展到多个 Web 应用实例，但这种扩展是特定于某个区域的。 高流量区域和低流量区域的扩展方式不同。

要将流量复制并路由到多个 MySQL 数据库，可使用 [ClearDB 高可用性路由器 (CDBR)][cleardbscale]（左侧所示）或 [MySQL 群集运营商级版本 (CGE)][cge]。

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>使用媒体存储和缓存的多区域部署
如果该站点接受上传或主机媒体文件，请使用 Azure Blob 存储。 如果需要缓存，请考虑使用 [Redis 缓存][rediscache]、[Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/)、[MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) 或 [Azure 应用商店](http://azure.microsoft.com/gallery/store/)中的一款其他缓存产品。

![一个托管在多个区域的 Azure Web 应用，使用适用于 MySQL 的 CDBR 高可用性路由器，带托管缓存、Blob 存储和内容传送网络][performance-diagram]

在默认情况下 Blob 存储分散在各个地区，因此无需担心跨所有站点复制文件的问题。 也可为 Blob 存储启用 Azure [内容传送网络][cdn]，这样可将文件分发至距离访客更近的终端节点。

### <a name="planning"></a>规划
#### <a name="additional-requirements"></a>其他要求
| 要执行以下操作... | 请使用以下方法... |
| --- | --- |
| **上传或存储大型文件** |[适用于使用 Blob 存储的 WordPress 插件][storageplugin] |
| **发送电子邮件** |[SendGrid][storesendgrid] 和[适用于使用 SendGrid 的 WordPress 插件][sendgridplugin] |
| **自定义域名** |[在 Azure 应用服务中配置自定义域名][customdomain] |
| **HTTPS** |[在 Azure 应用服务中启用 Web 应用的 HTTPS][httpscustomdomain] |
| **预生产验证** |[为 Azure 应用服务中的 Web 应用设置过渡环境][staging] <p>将 Web 应用从过渡移到生产时，还将移动 WordPress 配置。 将过渡应用移动到生产之前，请确保所有设置均针对生产应用的要求进行了更新。</p> |
| **监视和故障排除** |[在 Azure 应用服务中启用 Web 应用的诊断日志][log]和[在 Azure 应用服务中监视 Web 应用][monitor] |
| **部署站点** |[在 Azure 应用服务中部署 Web 应用][deploy] |

#### <a name="availability-and-disaster-recovery"></a>可用性和灾难恢复
| 要执行以下操作... | 请使用以下方法... |
| --- | --- |
| **对站点进行负载均衡**或**对站点进行地理分配** |[通过 Azure 流量管理器路由流量][trafficmanager] |
| **备份和还原** |[在 Azure 应用服务中备份 Web 应用][backup]和[在 Azure 应用服务中还原 Web 应用][restore] |

#### <a name="performance"></a>性能
云中的性能主要通过缓存和横向扩展实现。但是，还应考虑托管 Web 应用的内存、带宽和其他属性。

| 要执行以下操作... | 请使用以下方法... |
| --- | --- |
| **了解应用服务实例功能** |[定价详细信息，其中包括应用服务层的功能][websitepricing] |
| **缓存资源** |[Redis 缓存][rediscache]、[Memcache Cloud](/gallery/store/garantiadata/memcached/)、[MemCachier](/gallery/store/memcachier/memcachier/) 或 [Azure 应用商店](/gallery/store/)中的一款其他缓存产品 |
| **缩放应用程序** |[在 Azure 应用服务中扩展 Web 应用][websitescale]和 [ClearDB 高可用性路由][cleardbscale]。 如果选择托管和管理自己的 MySQL 安装，应考虑使用 [MySQL 群集 CGE][cge] 进行向外扩展。 |

#### <a name="migration"></a>迁移
要将现有 WordPress 站点迁移到 Azure 应用服务，可使用两种方法：

* **[WordPress 导出][export]**：此方法可导出博客的内容。 然后可使用 [WordPress 导入程序插件][import]，将该内容导入到 Azure 应用服务上的新 WordPress 站点。

  > [!NOTE]
  > 尽管此过程允许迁移内容，但不会迁移任何插件、主题或其他自定义内容。 必须再次手动安装这些组件。
  >
  >
* **手动迁移**：[备份站点][wordpressbackup]和[数据库][wordpressdbbackup]，然后手动将其还原到 Azure 应用服务中的 Web 应用和关联的 MySQL 数据库。 此方法可用于迁移高度自定义的站点，因为它可避免一些麻烦，如手动安装插件、主题和其他自定义内容。

## <a name="step-by-step-instructions"></a>分步说明
### <a name="create-a-wordpress-site"></a>创建 WordPress 站点
1. 在将托管站点的区域中，使用 [Azure 应用商店][cdbnstore]创建你在[架构与规划](#planning)部分中标识的相应大小的 MySQL 数据库。
2. 按照[在 Azure 应用服务中创建 WordPress Web 应用][createwordpress]中的步骤，创建一个 WordPress Web 应用。 创建 Web 应用时，选择**使用现有的 MySQL 数据库**，并选择在步骤 1 中创建的数据库。

如果要迁移现有 WordPress 站点，在创建新 Web 应用后，请参阅[将现有 WordPress 站点迁移到 Azure](#Migrate-an-existing-WordPress-site-to-Azure)。

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>将现有 WordPress 网站迁移到 Azure
如[架构与规划](#planning)部分所述，有两种方法可迁移 WordPress 站点：

* **使用导出和导入**迁移不含大量自定义内容或只需移动内容的站点。
* **使用备份和还原**迁移包含大量自定义内容并需要移动所有内容的站点。

使用下列部分中的一个迁移网站。

#### <a name="the-export-and-import-method"></a>导出和导入方法
1. 使用 [WordPress 导出][export]导出现有站点。
2. 使用[创建 WordPress 站点](#Create-a-new-WordPress-site)部分的步骤，创建 Web 应用。
3. 在 [Azure 门户][mgmtportal]上登录 WordPress 站点，并单击“插件” > “新增”。 搜索并安装 **WordPress 导入程序**插件。
4. 安装 WordPress 导入程序插件后，单击“工具” > “导入”，并单击“WordPress”使用 WordPress 导入程序插件。
5. 在“导入 WordPress”页面上，单击“选择文件”。 查找从现有 WordPress 站点导出的 WXR 文件，并单击“上传文件和导入”。
6. 单击“提交”。 系统会提示导入成功。
7. 完成所有这些步骤后，从 [Azure 门户][mgmtportal]中的“应用程序服务”边栏选项卡重启站点。

导入站点后，请执行以下步骤，启用导入文件中不包含的设置。

| 如果在使用... | 请执行以下操作... |
| --- | --- |
| **固定链接** |从新站点的 WordPress 仪表板中，单击“设置” > “固定链接”，并更新固定链接结构。 |
| **图像/媒体链接** |要将链接更新到新位置，请使用搜索和替换工具 [Velvet Blues Update URLs 插件][velvet]，或手动更新数据库中的链接。 |
| **主题** |转到“外观” > “主题”，并根据需要更新站点主题。 |
| **菜单** |如果主题支持菜单，那么主页的链接可能仍有嵌入的旧子目录。 转到“外观” > “菜单”，并对其进行更新。 |

#### <a name="the-backup-and-restore-method"></a>备份和还原方法
1. 使用 [WordPress 备份][wordpressbackup]上的信息，备份现有 WordPress 站点。
2. 使用[备份数据库][wordpressdbbackup]中的信息，备份现有数据库。
3. 创建数据库并还原备份。

   1. 从 [Azure 应用商店][cdbnstore]购买新的数据库，或者在 [Windows][mysqlwindows] 或 [Linux][mysqllinux] 虚拟机上设置 MySQL 数据库。
   2. 使用 MySQL 客户端（如 [MySQL Workbench][workbench]）连接到新数据库，并导入 WordPress 数据库。
   3. 更新数据库，将域条目更改为新的 Azure 应用服务域（例如 mywordpress.azurewebsites.net）。 使用[搜索和替换为 WordPress 数据库脚本][searchandreplace]，安全地更改所有实例。
4. 在 Azure 门户中创建 Web 应用并发布 WordPress 备份。

   1. 若要创建带数据库的 Web 应用，请在 [Azure 门户][mgmtportal]中，单击“新建” > “Web + 移动” > “Azure 应用商店” > “Web 应用” > “Web 应用 + SQL”（或“Web 应用 + MySQL”）>“创建”。 配置所有所需的设置来创建空 Web 应用。
   2. 在 WordPress 备份中，找到 **wp-config.php** 文件，并在编辑器中将其打开。 将以下条目替换为新 MySQL 数据库的信息：

      * **DB_NAME**：数据库的用户名。
      * **DB_USER**：用于访问数据库的用户名。
      * **DB_PASSWORD**：用户密码。

        更改这些条目后，请保存并关闭 **wp-config.php** 文件。
   3. 使用[在 Azure 应用服务中部署 Web 应用][deploy]信息，启用要使用的部署方法，然后将 WordPress 备份部署到 Azure 应用服务中的 Web 应用。
5. 部署 WordPress 站点后，应能使用站点的 *. azurewebsite.net URL（作为应用服务 Web 应用）访问新站点。

### <a name="configure-your-site"></a>配置网站
创建 WordPress 网站或将其迁移之后，可以使用以下信息来改进性能或启用其他功能。

| 要执行以下操作... | 请使用以下方法... |
| --- | --- |
| **设置应用服务计划模式、大小和启用缩放** |[在 Azure 应用服务中缩放 Web 应用][websitescale]。 |
| **启用持久的数据库连接** |默认情况下，WordPress 不使用持久的数据库连接，这可能导致数据库的连接在多次连接后受到限制。 若要启用持久连接，请安装[持久连接适配器插件](https://wordpress.org/plugins/persistent-database-connection-updater/installation/)。 |
| **提高性能** |<ul><li><p><a href="https://azure.microsoft.com/en-us/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/">禁用 ARR Cookie</a> 可在多个 Web 应用实例上运行 WordPress 时提高性能。</p></li><li><p>启用缓存。 可将 <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis 缓存</a>（预览版）与 <a href="https://wordpress.org/plugins/redis-object-cache/">Redis 对象缓存 WordPress 插件</a>配合使用，或可使用 <a href="/gallery/store/">Azure 应用商店</a>中的一款其他缓存产品。</p></li><li><p>[使用 Wincache 加快 WordPress 的速度](https://wordpress.org/plugins/w3-total-cache/)。 默认情况下为 Web 应用启用 Wincache。 配合使用 WinCache 和动态缓存时，请关闭 WinCache 的文件缓存，但让用户和会话缓存处于启用状态。 若要关闭文件缓存，请在系统级 .ini 文件中设置以下值：<br/><code>wincache.fcenabled = 0</code></p></li><li><p>[在 Azure 应用服务中缩放 Web 应用][websitescale]并使用 <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB 高可用性路由</a>或 <a href="http://www.mysql.com/products/cluster/">MySQL 群集 CGE</a>。</p></li></ul> |
| **使用 blob 作为存储** |<ol><li><p>[创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。</p></li><li><p>了解如何[使用内容分发网络 (CDN)](../cdn/cdn-create-new-endpoint.md) 将 blob 中存储的数据分发到各个地区。</p></li><li><p>安装并配置<a href="https://wordpress.org/plugins/windows-azure-storage/">适用于 WordPress 的 Azure 存储插件</a>。</p><p>有关该插件的详细设置和配置信息，请参阅<a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">用户指南</a>。</p> </li></ol> |
| **启用电子邮件** |使用 Azure 应用商店启用 <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a>。 安装适用于 WordPress 的 <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">SendGrid 插件</a>。 |
| **配置自定义域名** |[在 Azure 应用服务中配置自定义域名][customdomain]。 |
| **为自定义域名启用 HTTPS** |[在 Azure 应用服务中启用 Web 应用的 HTTPS][httpscustomdomain]。 |
| **对站点进行负载均衡或地理分配** |[通过 Azure 流量管理器路由流量][trafficmanager]。 如果使用自定义域，请参阅[在 Azure 应用服务中配置自定义域名][customdomain]，了解如何使用含自定义域名的流量管理器。 |
| **启用自动备份** |[在 Azure 应用服务中备份 Web 应用][backup]。 |
| **启用诊断日志记录** |[在 Azure 应用服务中启用 Web 应用的诊断日志记录][log]。 |

## <a name="next-steps"></a>后续步骤
* [WordPress 优化](http://codex.wordpress.org/WordPress_Optimization)
* [在 Azure 应用服务中将 WordPress 转换为多站点](web-sites-php-convert-wordpress-multisite.md)
* [面向 Azure 的 ClearDB 升级向导](http://www.cleardb.com/store/azure/upgrade)
* [在 Azure 应用服务中在 Web 应用的子文件夹中托管 WordPress](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)
* [分步说明：使用 Azure 创建 WordPress 站点](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)
* [在 Azure 上托管现有的 WordPress 博客](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)
* [在 WordPress 中启用美观的固定链接](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)
* [如何在 Azure 应用服务上迁移和运行 WordPress 博客](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)
* [如何在 Azure 应用服务上免费运行 WordPress](http://architects.dzone.com/articles/how-run-wordpress-azure)
* [在 2 分钟或更短时间内在 Azure 上运行 WordPress](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)
* [将 WordPress 博客移至 Azure - 第 1 部分：在 Azure 上创建 WordPress 博客](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)
* [将 WordPress 博客移至 Azure - 第 2 部分：传输内容](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)
* [将 WordPress 博客移至 Azure-第 3 部分：设置自定义域](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)
* [将 WordPress 博客移至 Azure - 第 4 部分：美观的固定链接和 URL 重写规则](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)
* [将 WordPress 博客移至 Azure - 第 5 部分：将一个子文件夹移动到根](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)
* [如何在 Azure 帐户中设置 WordPress Web 应用](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)
* [在 Azure 上支持 WordPress](http://www.johnpapa.net/wordpress-on-azure/)
* [在 Azure 上支持 WordPress 的技巧](http://www.johnpapa.net/azurecleardbmysql/)

> [!NOTE]
> 如果想要在注册 Azure 帐户之前开始使用 Azure 应用服务，请转到[试用应用服务](https://azure.microsoft.com/try/app-service/)，可以通过该页面在应用服务中立即创建一个生存期较短的入门 Web 应用。 不需要使用任何信用卡，也无需做出任何承诺。
>
>

## <a name="whats-changed"></a>发生的更改
有关从网站更改为应用服务的指南，请参阅 [Azure 应用服务及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)。

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: app-service-web-tutorial-custom-domain.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: app-service-web-tutorial-custom-ssl.md
[mysqlwindows]:../virtual-machines/windows/classic/mysql-2008r2.md
[mysqllinux]:../virtual-machines/linux/classic/mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: /powershell/azureps-cmdlets-docs
[Azure CLI]:../cli-install-nodejs.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md

