---
title: 新建 Azure Application Insights 资源 | Microsoft Docs
description: 为新的实时应用程序手动设置 Application Insights 监视。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073289"
---
# <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

Azure Application Insights 在 Microsoft Azure *资源*中显示有关应用程序的数据。 因此创建新资源属于[设置 Application Insights 来监视新应用程序][start]的一个环节。 创建新的资源后，可以获取其检测密钥并使用它来配置 Application Insights SDK。 检测密钥链接到该资源的遥测数据。

## <a name="sign-in-to-microsoft-azure"></a>登录到 Microsoft Azure

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

登录到[Azure 门户](https://portal.azure.com)，并创建 Application Insights 资源：

![单击左上角的 + 号。 选择 Application Insights 后跟的开发人员工具](./media/create-new-resource/new-app-insights.png)

   | 设置        |  值           | 说明  |
   | ------------- |:-------------|:-----|
   | **名称**      | 全局唯一值 | 标识正在监视的应用的名称。 |
   | **资源组**     | myResourceGroup      | 到托管 App Insights 数据的新的或现有资源组的名称。 |
   | **位置** | 美国东部 | 选择靠近你的位置或离托管您的应用程序的位置。 |

为所需的字段中，输入适当的值，然后选择**查看 + 创建**。

![向必填字段中输入值，然后选择"查看 + 创建"。](./media/create-new-resource/review-create.png)

创建您的应用程序后，将打开一个新窗格。 此窗格是您此处查看有关监视的应用程序性能和使用情况数据。 

## <a name="copy-the-instrumentation-key"></a>复制检测密钥

检测密钥标识你想要将遥测数据与相关联的资源。 您将需要复制将检测密钥添加到应用程序的代码。

![单击并复制检测密钥](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>在应用中安装 SDK

在应用中安装 Application Insights SDK。 此步骤在很大程度上依赖于应用程序的类型。

使用检测密钥来配置[在应用程序中安装的 SDK][start]。

SDK 包括发送遥测数据，而无需编写任何其他代码的标准模块。 若要跟踪用户操作或更细致地诊断问题，请[使用 API][api] 发送自己的遥测数据。

## <a name="creating-a-resource-automatically"></a>自动创建资源
可以编写 [PowerShell 脚本](../../azure-monitor/app/powershell.md)来自动创建资源。

## <a name="next-steps"></a>后续步骤
* [诊断搜索](../../azure-monitor/app/diagnostic-search.md)
* [探索指标](../../azure-monitor/app/metrics-explorer.md)
* [编写分析查询](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md