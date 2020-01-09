---
title: 从 Azure Application Insights 发送警报 | Microsoft Docs
description: 本教程介绍如何使用 Azure Application Insights 发送警报以响应应用程序中的错误。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: e45e6fb38296c5f3337f9edfb50cd9548ca5e210
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75398448"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>使用 Azure Application Insights 监视和警报应用程序的运行状况

Azure Application Insights 可用于监视应用程序，并在其不可用、遇到故障或性能问题时发送警报。  本教程将指导你创建测试，以便连续检查应用程序的可用性。

学习如何：

> [!div class="checklist"]
> * 创建可用性测试以持续检查应用程序的响应
> * 出现问题时向管理员发送邮件

## <a name="prerequisites"></a>必备条件

完成本教程：

创建 [Application Insights 资源](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-availability-test"></a>创建可用性测试

通过 Application Insights 中的可用性测试，可以从世界各地自动测试应用程序。   在本教程中，你将执行一项 URL 测试，以确保 Web 应用程序可用。  还可以创建完整的演练以测试其详细运行状况。 

1. 选择“Application Insights”，然后选择订阅  。  

2. 选择“调查”菜单下的“可用性”，然后单击“创建测试”    。

    ![添加可用性测试](media/tutorial-alert/add-test-001.png)

3. 键入测试名称，保留其他默认值。  此项选择将从五个不同的地理位置，每隔 5 分钟针对应用程序 URL 触发请求。

4. 选择“警报”打开“警报”下拉列表，可在其中定义测试失败时如何做出响应   。 选择“Near-realtime”并将状态设置为“已启用”。  

    键入满足警报条件时要发送到的电子邮件地址。  可根据需要键入满足警报条件时要调用的 webhook 的地址。

    ![创建测试](media/tutorial-alert/create-test-001.png)

5. 返回测试面板，选择省略号并编辑警报，以输入 near-realtime 警报的配置。

    ![编辑警报](media/tutorial-alert/edit-alert-001.png)

6. 将失败的位置数设置为大于或等于 3。 创建一个[操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)，用于配置当达到警报阈值时向谁发出通知。

    ![保存警报 UI](media/tutorial-alert/save-alert-001.png)

7. 配置警报后，请单击测试名称以查看每个位置的详细信息。 可以在折线图和散点图中查看测试，其中会直观显示给定时间范围的成功/失败结果。

    ![测试详细信息](media/tutorial-alert/test-details-001.png)

8. 可以通过单击散点图中的点深入到任一测试的详细信息。 这会启动端到端的事务详细信息视图。 下面的示例显示了失败请求的详细信息。

    ![测试结果](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>后续步骤

既已了解如何对问题设置警报，可继续学习下一个教程，了解如何分析用户与应用程序的交互。

> [!div class="nextstepaction"]
> [了解用户](../../azure-monitor/learn/tutorial-users.md)
