---
title: "向上缩放 Azure 中的应用 | Microsoft Docs"
description: "了解如何向上缩放 Azure 应用服务中的应用以增加容量和功能。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: c7989c3639e0fe3fd1f59dca98e5614f412e9593
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---
# <a name="scale-up-an-app-in-azure"></a>向上缩放 Azure 中的应用
本文介绍如何在 Azure 应用服务中缩放应用。 缩放的工作流有两种：向上缩放和向外缩放；本文介绍向上缩放工作流。

* [向上缩放](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：获取更多 CPU、内存、磁盘空间和额外功能，例如专用虚拟机 (VMs)、自定义域和证书、暂存槽、自动缩放以及更多功能。 可以通过更改应用所属的应用服务计划的定价层来向上缩放。
* [向外缩放](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：增加用于运行应用的 VM 实例数。
  最多可以向外缩放到 20 个实例，具体取决于定价层。 **高级**层中的 [应用服务环境](environment/intro.md)会进一步将向外缩放计数增加到 50 个实例。 有关向外缩放的详细信息，请参阅[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md)。 可以在该文中了解如何使用自动缩放，即根据预定义的规则和计划自动缩放实例计数。

缩放设置仅需几秒即可应用，并且会影响[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)中的所有应用。
在此过程中，不需要更改代码或重新部署应用程序。

有关各个应用服务计划的定价和功能的信息，请参阅[应用服务定价详细信息](https://azure.microsoft.com/pricing/details/web-sites/)。  

> [!NOTE]
> 在从**免费**层切换应用服务计划之前，必须首先删除对 Azure 订阅施加的[支出限制](https://azure.microsoft.com/pricing/spending-limits/)。 若要查看或更改 Microsoft Azure 应用服务订阅的选项，请参阅 [Microsoft Azure 订阅][azuresubscriptions]。
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>向上缩放定价层
1. 在浏览器中，打开 [Azure 门户][portal]。
2. 在应用的边栏选项卡中，单击“所有设置”，并单击“向上缩放”。
   
    ![导航到向上缩放 Azure 应用。][ChooseWHP]
3. 选择层，并单击“选择”。
   
    在操作完成后，“通知”选项卡上将闪现绿色的**成功**字样。

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>与缩放相关的资源
如果应用依赖于其他服务，如 Azure SQL 数据库或 Azure 存储，则还可以按需向上缩放这些资源。 不应将这些资源与应用服务计划一起缩放，而必须单独进行缩放。

1. 在“软件包”中，单击“资源组”链接。
   
    ![与向上缩放 Azure 应用相关的资源](./media/web-sites-scale/RGEssentialsLink.png)
2. 在“资源组”边栏选项卡的“摘要”部分中，单击要缩放的某个资源。 以下屏幕截图显示了 SQL 数据库资源和 Azure 存储资源。
   
    ![导航到资源组边栏选项卡以向上缩放 Azure 应用](./media/web-sites-scale/ResourceGroup.png)
3. 对于 SQL 数据库资源，请单击“设置” > “定价层”以缩放定价层。
   
    ![向上缩放 Azure 应用的 SQL 数据库后端](./media/web-sites-scale/ScaleDatabase.png)
   
    还可以为 SQL 数据库实例启用[异地复制](../sql-database/sql-database-geo-replication-overview.md)。
   
    对于 Azure 存储资源，请单击“设置” > “配置”以向上缩放存储选项。
   
    ![向上缩放 Azure 应用使用的 Azure 存储帐户](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>

## <a name="learn-about-developer-features"></a>了解开发人员功能
根据定价层提供了以下面向开发人员的功能：

### <a name="bitness"></a>位数
* **基本**、**标准**和**高级**层支持 64 位和 32 位应用程序。
* **免费**和**共享**计划层仅支持 32 位应用程序。

### <a name="debugger-support"></a>调试器支持
* 对于**免费**、**共享**和**基本**模式，按每个应用服务计划一个连接的形式提供调试器支持。
* 对于**标准**和**高级**模式，按每个应用服务计划五个并发连接的形式提供调试器支持。

<a name="OtherFeatures"></a>

## <a name="learn-about-other-features"></a>了解其他功能
* 有关应用服务计划中的所有其余功能的详细信息，包括定价和所有用户（包括开发人员）感兴趣的功能，请参阅[应用服务定价详细信息](https://azure.microsoft.com/pricing/details/web-sites/)。

> [!NOTE]
> 如果想要在注册 Azure 帐户之前开始使用 Azure 应用服务，请转到[试用应用服务](https://azure.microsoft.com/try/app-service/)，可以通过该页面在应用服务中立即创建一个生存期较短的入门 Web 应用。 不需要使用任何信用卡，也无需做出任何承诺。
> 
> 

<a name="Next Steps"></a>

## <a name="next-steps"></a>后续步骤
* 若要开始使用 Azure，请参阅 [Microsoft Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。
* 有关定价、支持和 SLA 的信息，请访问以下链接。
  
    [数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Microsoft Azure 支持计划](https://azure.microsoft.com/support/plans/)
  
    [服务级别协议](https://azure.microsoft.com/support/legal/sla/)
  
    [SQL 数据库定价详细信息](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Microsoft Azure 的虚拟机和云服务大小][vmsizes]
  
    [应用服务定价详细信息](https://azure.microsoft.com/pricing/details/app-service/)
  
    [应用服务定价详细信息 - SSL 连接](https://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)
* 有关 Azure应用服务最佳实践的信息，包括构建可缩放和可复原的体系结构，请参阅 [Best Practices: Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)（最佳实践：Azure 应用服务 Web 应用）。
* 有关缩放应用服务应用的视频，请参阅以下资源：
  
  * [When to Scale Azure Websites](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)（何时缩放 Azure 网站）- 主讲人：Stefan Schackow
  * [Auto Scaling Azure Websites, CPU or Scheduled](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)（自动缩放 Azure 网站、CPU 或计划）- 主讲人：Stefan Schackow
  * [How Azure Websites Scale](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)（Azure 网站如何缩放）- 主讲人：Stefan Schackow

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

