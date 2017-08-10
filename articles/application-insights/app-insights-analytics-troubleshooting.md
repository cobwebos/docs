---
title: "Azure Application Insights 中的故障排除分析| Microsoft Docs"
description: "Application Insights Analytics 出现了问题？ 从此处开始。 "
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/11/2016
ms.author: sewhee
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 82a1888cca687ce60f811c2c3cec6b971d4a0461
ms.contentlocale: zh-cn
ms.lasthandoff: 04/12/2017

---
# <a name="troubleshoot-analytics-in-application-insights"></a>排查 Application Insights 中的分析问题
[Application Insights Analytics](app-insights-analytics.md) 出现了问题？ 从此处开始。 Analytics 是 Azure Application Insights 的强大搜索工具。

## <a name="limits"></a>限制
* 目前，查询结果限制为过去一周的数据。
* 我们已测试过的浏览器：最新版本的 Chrome、Edge 和 Internet Explorer。

## <a name="known-incompatible-browser-extensions"></a>已知不兼容的浏览器扩展
* Ghostery

请禁用该扩展，或使用不同的浏览器。

## <a name="e-a"></a>“意外的错误”
![“意外的错误”屏幕](./media/app-insights-analytics-troubleshooting/010.png)

在门户运行时期间发生内部错误 – 未经处理的异常。

* 清除浏览器的缓存。 

## <a name="e-b"></a>403 ... 请尝试重新加载
![403 ... 请尝试重新加载](./media/app-insights-analytics-troubleshooting/020.png)

发生身份验证相关的错误（在身份验证期间或生成访问令牌期间）。 如果不更改浏览器设置，门户可能无法恢复。

* 检查是否在浏览器中[启用了第三方 Cookie](#cookies)。 

## <a name="authentication"></a>403 ... 验证安全区域
![403 ... 验证安全区域](./media/app-insights-analytics-troubleshooting/030.png)

发生身份验证相关的错误（在身份验证期间或生成访问令牌期间）。 如果不更改浏览器设置，门户可能无法恢复。

1. 检查是否在浏览器中[启用了第三方 Cookie](#cookies)。 
2. 是否使用了收藏夹、书签或保存的链接来打开 Analytics 门户？ 登录时使用的凭据与保存链接时使用的凭据是否不同？
3. 尝试使用 InPrivate/incognito 浏览器窗口（在关闭所有此类窗口之后）。 需要提供凭据。 
4. 打开另一个（普通）浏览器窗口并转到 [Azure](https://portal.azure.com)。 注销。 然后打开链接并使用正确的凭据登录。
5. 如果受信任区域设置不受支持，Edge 和 Internet Explorer 的用户也会收到此错误。
   
    检查 [Analytics 门户](https://analytics.applicationinsights.io)和 [Azure Active Directory 门户](https://portal.azure.com)是否位于相同的安全区域：
   
   * 在 Internet Explorer 中，打开“Internet 选项”、“安全”、“受信任的站点”、“站点”：
     
     ![“Internet 选项”对话框，将站点添加到受信任的站点](./media/app-insights-analytics-troubleshooting/033.png)
     
     如果“网站”列表中包含以下任一 URL，请确保同时包含其他 URL：
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ...找不到资源
![404 ... 找不到资源](./media/app-insights-analytics-troubleshooting/040.png)

应用程序资源已从 Application Insights 中删除，不再可用。 如果将 URL 保存到了“Analytics”页，可能发生这种情况。

## <a name="e-e"></a>403 ...未授权
![403 ... 未授权](./media/app-insights-analytics-troubleshooting/050.png)

你无权在 Analytics 中打开此应用程序。

* 链接是其他人提供的吗？ 请求他们将你设置为[此资源组的读取者或参与者](app-insights-resources-roles-access-control.md)。
* 保存链接时是否使用了不同的凭据？ 请打开 [Azure 门户](https://portal.azure.com)，注销，提供正确的凭据，然后重试打开此链接。

## <a name="html-storage"></a>403 ...HTML5 存储
我们的门户使用 HTML5 localStorage 和 sessionStorage。

* Chrome：选择“设置”、“隐私”、“内容设置”。
* Internet  Explorer：选择“Internet 选项”、“高级”选项卡、“安全”、“启用 DOM 存储”

![403 ... 尝试启用 HTML5 存储](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ...找不到订阅
![404 ...找不到订阅](./media/app-insights-analytics-troubleshooting/070.png)

URL 无效。 

* 在 [Application Insights 门户](https://portal.azure.com)中打开应用资源。 然后使用“Analytics”按钮。

## <a name="e-h"></a>404 ... 页面不存在
![404 ...页面不存在](./media/app-insights-analytics-troubleshooting/080.png)

URL 无效。

* 在 [Application Insights 门户](https://portal.azure.com)中打开应用资源。 然后使用“Analytics”按钮。

## <a name="cookies"></a>启用第三方 Cookie
  请参阅[如何禁用第三方 Cookie](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers)，但请注意，我们需要**启用**它们。


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


