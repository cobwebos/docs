---
title: "有关在 Azure 门户中导航的参考"
description: "了解用户在管理门户和 Azure 门户之间对 App Service Web 的不同体验"
services: app-service
documentationcenter: 
author: jaime-espinosa
manager: wpickett
editor: jimbe
ms.assetid: 0cc6a3cc-bd89-4a96-9177-d25f6fb737bb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: jaime-espinosa
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 00617f4d508c843000d69c02464a1bef36320a8e


---
# <a name="reference-for-navigating-the-azure-portal"></a>有关在 Azure 门户中导航的参考
现在，Azure 网站称为[应用服务 Web 应用](http://go.microsoft.com/fwlink/?LinkId=529714)。 我们正在更新所有文档以反映此名称更改，并为 Azure 门户提供说明。 完成该过程之前，可以使用此文档作为在 Azure 门户中使用 Web 应用的指南。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="the-future-of-the-azure-classic-portal"></a>将来的 Azure 经典门户
如果发现 Azure 经典门户中品牌改变，该门户正处于被 Azure 门户替代的过程中。 由于正在逐步淘汰经典门户，因此新的开发工作重点将转移到 Azure 门户。 所有即将发布的 Web 应用新功能将出现在 Azure 门户中。 开始使用 Azure 门户以利用 Web 应用提供最新最好的功能。

## <a name="layout-differences-between-the-azure-classic-portal-and-azure-portal"></a>Azure 经典门户和 Azure 门户之间的布局差异
在经典门户中，所有 Azure 服务都列在左侧。 经典门户中的导航遵循树状结构，即从服务开始导航，然后再导航到各个元素。 此结构适用于管理独立组件。 但是，在 Azure 上构建的应用程序是互联服务的集合，而此树状结构并不适用于服务集合。 

Azure 门户则可以利用多个服务中的组件端对端地轻松构建应用程序。 该门户被组织为“旅程”。 “旅程”是一系列的“边栏选项卡”（即不同组件的容器）。 例如，设置 Web 应用的自动缩放即为一段“旅程”，该“旅程”包含若干边栏选项卡，如下例所示：“网站”边栏选项卡（该边栏选项卡标题尚未更新至使用新术语）、“设置”边栏选项卡和“扩大”边栏选项卡。 在此示例中，自动缩放被设置为依据 CPU 使用情况而定，因此还具有一个“CPU 百分比”边栏选项卡。 边栏选项卡中的组件被称为“部件”，外形像磁贴。 

![](./media/app-service-web-app-azure-portal/AutoScaling.png)

## <a name="navigation-example-create-a-web-app"></a>浏览示例：创建 Web 应用
创建新 Web 应用的步骤如 1-2-3 一样简单。 下图并排显示了经典门户和新门户，以便说明创建和运行 Web 应用所需的步骤数并未发生太大变化。 

![](./media/app-service-web-app-azure-portal/CreateWebApp.png)

在新门户中，你可以从最常用的 Web 应用类型中选择，包括 WordPress 等受欢迎的库应用程序。 有关可用应用程序的完整列表，请访问 [Azure 应用商店]。

创建 Web 应用时，可以如在经典门户中所执行的操作一样，在新门户中指定 URL、应用服务计划和位置。 

![](./media/app-service-web-app-azure-portal/CreateWebAppSettings.png)

此外，你可以在新门户中定义其他通用设置。 例如，通过[资源组](../azure-resource-manager/resource-group-overview.md)可以轻松查看和管理相关 Azure 资源。 

## <a name="navigation-example-settings-and-features"></a>浏览示例：设置和功能
现在所有设置和功能都逻辑分组在单个边栏选项卡中，进而方便从中进行导航。

![](./media/app-service-web-app-azure-portal/WebAppSettings.png)

例如，可以通过单击“设置”边栏选项卡中的“自定义域和 SSL”来创建自定义域。

![](./media/app-service-web-app-azure-portal/ConfigureWebApp.png)

若要设置监视警报，请单击“请求和错误”，然后单击“添加警报”。

![](./media/app-service-web-app-azure-portal/Monitoring.png)

若要启用诊断，请单击“设置”边栏选项卡中的“诊断日志”。

![](./media/app-service-web-app-azure-portal/Diagnostics.png)

若要配置应用程序设置，请单击“设置”边栏选项卡中的“应用程序设置”。 

![](./media/app-service-web-app-azure-portal/AppSettingsPreview.png)

除品牌名称之外，已对门户中的一些事物进行了重命名或进行了不同的分组，以便更容易查找。 例如，下面是经典门户中应用设置（**配置**）相应页的屏幕截图。

![](./media/app-service-web-app-azure-portal/AppSettings.png)

## <a name="more-resources"></a>更多资源
[Azure Portal]: https://portal.azure.com
[Azure 应用商店]: /marketplace/

> [!NOTE]
> 如果您想要在注册 Azure 帐户之前开始使用 Azure App Service，请转到 [试用 App Service](https://azure.microsoft.com/try/app-service/)，您可以在 App Service 中立即创建一个生存期较短的入门 Web 应用。 不需要使用信用卡，也不需要做出承诺。
> 
> 

## <a name="whats-changed"></a>发生的更改
* 有关从网站更改为 App Service 的指南，请参阅 [Azure App Service 及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)




<!--HONumber=Feb17_HO3-->


