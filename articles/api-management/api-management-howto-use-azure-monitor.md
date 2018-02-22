---
title: "在 Azure API 管理中监视已发布的 API | Microsoft Docs"
description: "遵循本教程的步骤了解如何在 Azure API 管理中监视 API。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 445723242a76dcef4a6b137439728235d5d6e32a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="monitor-published-apis"></a>监视已发布的 API

Azure Monitor 作为一项 Azure 服务，提供单一资源来监视所有 Azure 资源。 通过 Azure Monitor，可对来自 Azure 资源（例如 API 管理）的指标和日志进行可视化、查询、路由、存档和执行操作。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 查看活动日志
> * 查看诊断日志
> * 查看 API 的指标 
> * 针对 API 收到的未经授权的调用设置警报规则

下方视频介绍如何使用 Azure Monitor 监视 API 管理。 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>先决条件

+ 完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。
+ 此外，请完成以下教程：[导入并发布第一个 API](import-and-publish.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>查看活动日志

活动日志提供针对 API 管理服务执行的操作的详细信息。 通过活动日志，可确定对 API 管理服务执行的任何写入操作 (PUT、POST、DELETE) 的“操作内容、操作人员和操作时间”。 

> [!NOTE]
> 活动日志不包括读取 (GET) 操作或者通过 Azure 门户或原始管理 API 执行的操作。

可在 API 管理服务中访问活动日志，或在 Azure Monitor 中访问所有 Azure 资源的日志。 

查看活动日志：

1. 选择 APIM 服务实例。
2. 单击“活动日志”。

## <a name="view-diagnostic-logs"></a>查看诊断日志

诊断日志提供大量有关操作和错误的信息，这些信息对审核和故障排除非常重要。 诊断日志不同于活动日志。 活动日志提供针对 Azure 资源执行的操作的详细信息。 诊断日志提供资源本身执行的操作的深入信息。

访问诊断日志：

1. 选择 APIM 服务实例。
2. 单击“诊断日志”。

## <a name="view-metrics-of-your-apis"></a>查看 API 的指标

API 管理每分钟发出一次指标，几乎可让你实时了解 API 的状态和运行状况。 下面汇总了一些可用指标：

* 容量（预览版）：帮助做出有关升级/降级 APIM 服务的决策。 指标每分钟发出，在报告时反映网关容量。 指标范围为 0-100，是根据 CPU 和内存利用率等网关资源计算的。
* 网关请求总数：期间内的 API 请求数。 
* 成功的网关请求数：接收成功的 HTTP 响应代码（包括 304、307 以及任何小于 301 的代码，例如 200）的 API 请求数。 
* 失败的网关请求数：接收错误的 HTTP 响应代码（包括 400 以及任何大于 500 的代码）的 API 请求数。
* 未经授权的网关请求数：接收包括 401、403 和 429的 HTTP 响应代码的 API 请求数。 
* 其他网关请求数：接收不属于上述任何类别的 HTTP 响应代码（例如 418）的 API 请求数。

访问指标：

1. 在靠近页面底部的菜单中选择“指标”。
2. 从下拉列表中选择所需的指标（可以添加多个指标）。 
    
    例如，从可用指标列表中选择“网关请求总数”和“失败的网关请求数”。
3. 该图显示 API 调用总数。 此外还显示失败的 API 调用数。 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>针对未经授权的请求设置警报规则

可配置为基于指标和活动日志接收警报。 通过 Azure Monitor 可配置警报，使警报触发时执行以下操作：

* 发送电子邮件通知
* 调用 Webhook
* 调用 Azure 逻辑应用

配置警报：

1. 在靠近页面底部的菜单栏中选择“警报规则”。
2. 选择“添加指标警报”。
3. 输入此警报的**名称**。
4. 选择“未经授权的网关请求”作为要监视的指标。
5. 选择“电子邮件所有者、参与者和阅读者”。
6. 按“确定”。
7. 尝试在不使用 API 密钥的情况下调用会议 API。 此 API 管理服务的所有者会收到电子邮件警报。 

    > [!TIP]
    > 警报规则在触发后还可调用 Web 挂钩或 Azure 逻辑应用。

    ![set-up-alert](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 查看活动日志
> * 查看诊断日志
> * 查看 API 的指标 
> * 针对 API 收到的未经授权的调用设置警报规则

转到下一教程：

> [!div class="nextstepaction"]
> [跟踪调用](api-management-howto-api-inspector.md)
