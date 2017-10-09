---
title: "从 Azure Application Insights 发送警报 | Microsoft Docs"
description: "本教程介绍如何使用 Azure Application Insights 发送警报以响应应用程序中的错误。"
keywords: 
author: bwren
ms.author: bwren
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 40b3cd74fc68158b679db462278edef4827d8603
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>使用 Azure Application Insights 监视和警报应用程序的运行状况

Azure Application Insights 可用于监视应用程序，并在其不可用、遇到故障或性能问题时发送警报。  本教程将指导你创建测试，该测试可连续检查应用程序的可用性，并发送不同类型的警报以响应检测到的问题。  学习如何：

> [!div class="checklist"]
> * 创建可用性测试以持续检查应用程序的响应
> * 出现问题时向管理员发送邮件
> * 根据性能指标创建警报 
> * 按计划使用逻辑应用发送汇总的遥测。


## <a name="prerequisites"></a>先决条件

完成本教程：

- 使用以下工作负荷安装 [Visual Studio 2017](https://www.visualstudio.com/downloads/)：
    - ASP.NET 和 Web 开发
    - Azure 开发
    - 将 .NET 应用程序部署到 Azure 并[启用 Application Insights SDK](app-insights-asp-net.md)。 


## <a name="log-in-to-azure"></a>登录 Azure
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。

## <a name="create-availability-test"></a>创建可用性测试
通过 Application Insights 中的可用性测试，可以从世界各地自动测试应用程序。   在本教程中，你将执行简单测试，以确保应用程序可用。  还可以创建完整的演练以测试其详细运行状况。 

1. 选择“Application Insights”，然后选择订阅。  
1. 选择“调查”菜单下的“可用性”，然后单击“添加测试”。
 
    ![添加可用性测试](media/app-insights-tutorial-alert/add-test.png)

2. 键入测试名称，保留其他默认值。  这会从 5 个不同的地理位置每 5 分钟对应用程序主页发送请求。 
3. 选择“警报”以打开“警报”面板，可以在其中定义测试失败时如何响应。 键入满足警报条件时要发送到的电子邮件地址。  可根据需要键入满足警报条件时要调用的 webhook 的地址。

    ![创建测试](media/app-insights-tutorial-alert/create-test.png)
 
4. 返回到测试面板，几分钟后应可开始看到可用性测试的结果。  单击测试名称以查看每个位置的详细信息。  散点图显示了每个测试的成功与否和持续时间。

    ![测试详细信息](media/app-insights-tutorial-alert/test-details.png)

5.  通过单击散点图中的点，可以进一步了解任何特定测试的详细信息。  下面的示例显示了失败请求的详细信息。

    ![测试结果](media/app-insights-tutorial-alert/test-result.png)
  
6. 如果满足警报条件，将向指定的地址发送类似如下的邮件。

    ![警报电子邮件](media/app-insights-tutorial-alert/alert-mail.png)


## <a name="create-an-alert-from-metrics"></a>从指标创建警报
除了由可用性测试发送警报，还可由从应用程序收集的任何性能指标创建警报。

2. 从“配置”菜单选择“警报”。  这将打开 Azure 警报面板。  此处可能有为其他服务配置的警报规则。
3. 单击“添加指标警报”。  这将打开创建新警报规则的面板。

    ![添加指标警报](media/app-insights-tutorial-alert/add-metric-alert.png)

4. 键入警报规则的“名称”，然后在“资源”下拉列表中选择应用程序。
5. 选择要采样的“指标”。  所示图表指示过去 24 小时内此请求的值。  这有可以帮助你为指标设置条件。

    ![添加警报规则](media/app-insights-tutorial-alert/add-alert-01.png)

6. 指定警报的“条件”和“阈值”。 必须有这么多次超过指标，才会创建警报。 
6. 在“通知方式”下，选中“电子邮件所有者、参与者和读者”框，并添加其他任何收件人的电子邮件地址，满足警报条件时，将向这些用户发送邮件。  还可以在此处指定满足条件时运行的 webhook 或逻辑应用。  可使用它们尝试缓解检测到的问题或 

    ![添加警报规则](media/app-insights-tutorial-alert/add-alert-02.png)


## <a name="proactively-send-information"></a>主动发送信息
在应用程序中发现一组特定问题时即会创建警报，通常要对需立即关注的关键条件设置警报。  可以通过按计划自动运行的逻辑应用，主动接收关于应用程序的信息。  例如，可以每天向管理员发送一封邮件，提供需进一步评估的摘要信息。

若要了解如何创建使用 Application Insights 的逻辑应用，请参阅[使用逻辑应用自动执行 Application Insights 进程](automate-with-logic-apps.md)

## <a name="next-steps"></a>后续步骤
既已了解如何对问题设置警报，可继续学习下一个教程，了解如何分析用户与应用程序的交互。

> [!div class="nextstepaction"]
> [了解用户](app-insights-tutorial-users.md)
