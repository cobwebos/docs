---
title: "演练：使用 Application Insights 监视 Microsoft Dynamics CRM"
description: "使用 Application Insights 从 Microsoft Dynamics CRM Online 获取遥测。 设置、获取数据、可视化和导出的演练。"
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 9304b26711226fc9a7e672f59441ae65c0d5a023


---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>演练：使用 Application Insights 为 Microsoft Dynamics CRM Online 启用遥测
本文演示如何使用 [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 从 [Microsoft Dynamics CRM Online](https://www.dynamics.com/) 获取遥测数据。 我们将引导你完成向应用程序添加 Application Insights 脚本、捕获数据和数据可视化的完整过程。

> [!NOTE]
> [浏览示例解决方案](https://dynamicsandappinsights.codeplex.com/)。
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>向新的或现有的 CRM Online 实例添加 Application Insights
若要监视应用程序，则将 Application Insights SDK 添加到应用程序。 SDK 会将遥测发送到 [Application Insights 门户](https://portal.azure.com)，可以在其中使用我们功能强大的分析和诊断工具，或将数据导出到存储。

### <a name="create-an-application-insights-resource-in-azure"></a>在 Azure 中创建 Application Insights 资源
1. 获取 [Microsoft Azure 中的帐户](http://azure.com/pricing)。 
2. 登录到 [Azure 门户](https://portal.azure.com)，并添加新的 Application Insights 资源。 这是将处理和显示数据的位置。
   
    ![依次单击“+”、“开发人员服务”、“Application Insights”。](./media/app-insights-sample-mscrm/01.png)
   
    选择 ASP.NET 作为应用程序类型。
3. 打开“快速启动”选项卡并打开代码脚本。
   
    ![](./media/app-insights-sample-mscrm/03.png)

**使代码页保持打开状态**，同时在另一浏览器窗口上执行下一步。 你将很快就会需要代码。 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>在 Microsoft Dynamics CRM 中创建 JavaScript Web 资源
1. 打开 CRM Online 实例并使用管理员特权登录。
2. 打开“Microsoft Dynamics CRM 设置”、“自定义”、“自定义系统”
   
    ![](./media/app-insights-sample-mscrm/04.png)
   
    ![](./media/app-insights-sample-mscrm/05.png)

    ![](./media/app-insights-sample-mscrm/06.png)

1. 创建 JavaScript 资源。
   
    ![](./media/app-insights-sample-mscrm/07.png)
   
    为它指定名称、选择“脚本 (JScript)”，并打开文本编辑器。
   
    ![](./media/app-insights-sample-mscrm/08.png)
2. 从 Application Insights 复制代码。 复制时确认忽略脚本标记。 请参阅下面的屏幕快照：
   
    ![](./media/app-insights-sample-mscrm/09.png)
   
    代码包括用于标识 Application insights 资源的检测密钥。
3. 保存并发布。
   
    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>检测窗体
1. 在 Microsoft CRM Online 中，打开帐户窗体
   
    ![](./media/app-insights-sample-mscrm/11.png)
2. 打开窗体属性
   
    ![](./media/app-insights-sample-mscrm/12.png)
3. 添加已创建的 JavaScript Web 资源
   
    ![](./media/app-insights-sample-mscrm/13.png)
   
    ![](./media/app-insights-sample-mscrm/14.png)
4. 保存并发布窗体自定义。

## <a name="metrics-captured"></a>指标捕获
现在，已经为窗体设置了遥测捕获。 只要使用它，数据将发送到 Application Insights 资源。

下面是你将看到的数据示例。

#### <a name="application-health"></a>应用程序运行状况
![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

浏览器异常：

![](./media/app-insights-sample-mscrm/17.png)

单击图表以获取更多详细信息：

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>使用情况
![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>浏览器
![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>地理位置
![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>深入了解页面查看请求
![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>代码示例
[浏览示例代码](https://dynamicsandappinsights.codeplex.com/)。

## <a name="power-bi"></a>Power BI
如果[将数据导出到 Microsoft Power BI](app-insights-export-power-bi.md)，则可以执行更深入的分析。

## <a name="sample-microsoft-dynamics-crm-solution"></a>Microsoft Dynamics CRM 解决方案示例
[此处是在 Microsoft Dynamics CRM 中实现的示例解决方案](https://dynamicsandappinsights.codeplex.com/)。

## <a name="learn-more"></a>了解详细信息
* [什么是 Application Insights？](app-insights-overview.md)
* [适用于网页的 Application Insights](app-insights-javascript.md)
* [更多示例和演练](app-insights-code-samples.md)




<!--HONumber=Nov16_HO3-->


