---
title: "如何从 Application Insights 开发团队获取技术支持 | Microsoft 文档"
description: "Application Insights Analytics 出现问题时请求帮助"
services: application-insights
documentationcenter: 
author: alexbulankou
manager: douge
ms.assetid: 39035206-c836-4dca-93df-5013b7753aed
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/01/2016
ms.author: albulank
translationtype: Human Translation
ms.sourcegitcommit: 7dbcc3b11b744361113d4dba100407ee85c40cac
ms.openlocfilehash: 330fab68efedee0e6e56459289f94beb88a5fdd5


---
# <a name="get-help-with-application-insights"></a>使用 Application Insights 时请求帮助
使用 [Azure Application Insights](app-insights-overview.md) 时如何遇到技术问题，可以使用下面的选项获取帮助：

## <a name="1-check-the-documents"></a>1.检查文档
* 是否缺少数据？ 检查：[采样](app-insights-sampling.md)、[配额和限制](app-insights-pricing.md)。
* 疑难解答：[ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## <a name="2-search-the-forums"></a>2.搜索论坛
* [MSDN 论坛](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## <a name="3-azure-support-plan"></a>3.是否使用了 Azure 支持计划？
有时，你希望开发人员能够调查你的具体案例。 

如果你已购买 [Microsoft Azure 支持计划](https://azure.microsoft.com/support/plans/)，则可以[开具一份支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="4-feedback-to-the-application-insights-team"></a>4.向 Application Insights 团队反馈
我们的开发团队很乐于倾听你所遇到的问题，尤其是上面的解决方法不能解决你的问题时。 请通过 Azure 门户上的表单提交反馈。 然后，Application Insights 团队中的开发人员将会受理该问题。

1. 在 [Application Insights 门户](https://portal.azure.com)中，单击右上角的笑脸图标：  
   
    ![反馈按钮](./media/app-insights-get-dev-support/01.png)   
2. 在备注框中，请务必在第一行指定 **AppInsights**，然后包含以下信息：   
   
    ```
   
    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    issue: <please describe the problem you are having>
   
    ```   
   
    ![反馈对话框](./media/app-insights-get-dev-support/02.png)   
3. 选中“是，可以向我发送电子邮件”。 
   
    ![提交部分](./media/app-insights-get-dev-support/03.png)  






<!--HONumber=Nov16_HO3-->


