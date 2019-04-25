---
title: Microsoft Dynamics CRM 和 Azure Application Insights | Microsoft docs
description: 使用 Application Insights 从 Microsoft Dynamics CRM Online 获取遥测。 设置、获取数据、可视化和导出的演练。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/16/2018
ms.reviewer: mazhar
ms.author: mbullwin
ms.openlocfilehash: 6119f1116d255f7cd2a2bfc20e86eeca9e5dfe82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60523488"
---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>演练：使用 Application Insights 为 Microsoft Dynamics CRM Online 启用遥测
本文演示如何使用 [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 从 [Microsoft Dynamics CRM Online](https://www.dynamics.com/) 获取遥测数据。 我们将引导完成向应用程序添加 Application Insights 脚本、捕获数据和数据可视化的完整过程。

> [!NOTE]
> [浏览示例解决方案](https://dynamicsandappinsights.codeplex.com/)。
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>向新的或现有的 CRM Online 实例添加 Application Insights
要监视应用程序，则将 Application Insights SDK 添加到应用程序。 SDK 会将遥测发送到 [Application Insights 门户](https://portal.azure.com)，可以在其中使用我们功能强大的分析和诊断工具，或将数据导出到存储。

### <a name="create-an-application-insights-resource-in-azure"></a>在 Azure 中创建 Application Insights 资源
1. 获取 [Microsoft Azure 中的帐户](https://azure.com/pricing)。 
2. 登录到 [Azure 门户](https://portal.azure.com)，并添加新的 Application Insights 资源。 这是将处理和显示数据的位置。

    ![依次单击“+”、“开发人员服务”、“Application Insights”。](./media/sample-mscrm/01.png)

    选择 ASP.NET 作为应用程序类型。
3. 按照说明[获取应用的 JavaScript SDK 脚本](../../azure-monitor/app/javascript.md#set-up-application-insights-for-your-web-page)，复制 JavaScript 代码片段并确保将检测密钥替换为 Application Insights 资源的正确值。

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>在 Microsoft Dynamics CRM 中创建 JavaScript Web 资源
1. 打开 CRM Online 实例并使用管理员特权登录。
2. 打开“Microsoft Dynamics CRM 设置”、“自定义”、“自定义系统”

    ![Microsoft Dynamics CRM 设置](./media/sample-mscrm/00001.png)

    ![设置 > 自定义项](./media/sample-mscrm/00002.png)

1. 创建 JavaScript 资源。

    ![“新建 Web 资源”对话框](./media/sample-mscrm/07.png)

    为它指定名称、选择“脚本 (JScript)”，并打开文本编辑器。

    ![打开文本编辑器](./media/sample-mscrm/00004.png)
2. 从之前已配置检测密钥的 Application Insights JavaScript SDK 复制代码。 复制时，请确保忽略脚本标记。 请参阅以下屏幕截图：

    ![设置检测密钥](./media/sample-mscrm/000005.png)

    代码包括用于标识 Application insights 资源的检测密钥。
3. 保存并发布。

    ![保存并发布](./media/sample-mscrm/00006.png)

### <a name="instrument-forms"></a>检测窗体
1. 在 Microsoft CRM Online 中，打开帐户窗体

    ![帐户窗体](./media/sample-mscrm/00007.png)
2. 打开窗体属性

    ![窗体属性](./media/sample-mscrm/00008.png)
3. 添加已创建的 JavaScript Web 资源

    ![添加菜单](./media/sample-mscrm/13.png)

4. 保存并发布窗体自定义。

## <a name="metrics-captured"></a>指标捕获
现在，已经为窗体设置了遥测捕获。 只要使用它，数据将发送到 Application Insights 资源。

下面是会看到的数据示例。

#### <a name="application-health"></a>应用程序运行状况
![加载时间示例页面](./media/sample-mscrm/15.png)

![视图图表示例页面](./media/sample-mscrm/16.png)

浏览器异常：

![浏览器异常图表](./media/sample-mscrm/17.png)

单击图表以获取更多详细信息：

![异常列表](./media/sample-mscrm/18.png)

#### <a name="usage"></a>使用情况
![用户、会话和页面视图](./media/sample-mscrm/19.png)

![会话图表](./media/sample-mscrm/20.png)

![浏览器版本](./media/sample-mscrm/21.png)

#### <a name="browsers"></a>浏览器
![页面加载时间明细](./media/sample-mscrm/22.png)

![按浏览器版本分类的会话数](./media/sample-mscrm/23.png)

#### <a name="geolocation"></a>地理位置
![按国家/地区分类的会话数](./media/sample-mscrm/24.png)

![按国家/地区分类的会话和用户](./media/sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>深入了解页面查看请求
![页面视图摘要](./media/sample-mscrm/26.png)

![搜索页面视图事件](./media/sample-mscrm/27.png)

![相似的页面视图](./media/sample-mscrm/28.png)

![页面视图属性](./media/sample-mscrm/29.png)

![每个会话的页面数](./media/sample-mscrm/30.png)

## <a name="sample-code"></a>代码示例
[浏览示例代码](https://dynamicsandappinsights.codeplex.com/)。

## <a name="power-bi"></a>Power BI
如果[将数据导出到 Microsoft Power BI](../../azure-monitor/app/export-power-bi.md )，则可以执行更深入的分析。

## <a name="sample-microsoft-dynamics-crm-solution"></a>Microsoft Dynamics CRM 解决方案示例
[此处是在 Microsoft Dynamics CRM 中实现的示例解决方案](https://dynamicsandappinsights.codeplex.com/)。

## <a name="learn-more"></a>了解详细信息
* [什么是 Application Insights？](../../azure-monitor/app/app-insights-overview.md)
* [适用于网页的 Application Insights](../../azure-monitor/app/javascript.md)
* [更多示例和演练](../../azure-monitor/app/app-insights-overview.md)
