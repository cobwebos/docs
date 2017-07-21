---
title: "Azure 中的自动缩放入门 | Microsoft Docs"
description: "了解如何在 Azure 中缩放资源。"
author: rajram
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: rajram
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 4faccae708b5407d1eb64e746824a23688bc5834
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017


---
# <a name="get-started-with-auto-scale-in-azure"></a>Azure 中的自动缩放入门
本文介绍如何在 Azure 门户中为资源指定自动缩放设置。

Azure Monitor 自动缩放仅适用于虚拟机规模集 (VMSS)、云服务、应用服务计划和应用服务环境。 

# <a name="lets-get-started"></a>入门

## <a name="discover-the-auto-scale-settings-in-your-subscriptions"></a>了解订阅中的自动缩放设置
可以在 Azure Monitor 中发现适用自动缩放的所有资源。 请遵循下面列出的步骤完成分步演练。

- 打开 [Azure 门户][1]
- 单击左侧导航窗格中的 Azure Monitor 图标。
  ![启动 Azure Monitor][2]
- 单击“自动缩放设置”可查看适用自动缩放的所有资源及其当前自动缩放状态![在 Azure Monitor 中发现自动缩放][3]

可以使用顶部的筛选器窗格来缩小列表的范围，以选择特定资源组中的资源、选择特定的资源类型或选择特定的资源。

对于每个资源，将会看到其当前实例计数和自动缩放状态。 自动缩放状态可以是

- 未配置：尚未对此资源启用自动缩放设置
- 已启用：对此资源启用了自动缩放设置
- 已禁用：对此资源禁用了自动缩放设置

## <a name="create-your-first-auto-scale-setting"></a>创建第一个自动缩放设置

现在，让我们完成一个简单的分步演练，以创建第一个自动缩放设置。

- 在 Azure Monitor 中打开“自动缩放”边栏选项卡，然后选择要缩放的资源。 （以下步骤使用的应用服务计划与某个 Web 应用相关联。 可以[在 Azure 中花费不超过 5 分钟的时间创建第一个 ASP.NET Web 应用][4]）
- 在资源的“缩放设置”边栏选项卡中，请注意当前实例计数为 1。 单击“启用自动缩放”。
  ![新 Web 应用的缩放设置][5]
- 提供缩放设置的名称，然后单击“添加规则”。 请注意右侧以上下文窗格形式打开的缩放规则选项。 默认情况下，选项设置为当资源的 CPU 百分比超过 70% 时，将实例计数缩放 1 个单位。 请将此选项保留默认值，然后单击“添加”。
  ![为 Web 应用创建缩放设置][6]
- 现已创建第一个缩放规则。 请注意，UX 建议了最佳做法，并指出“建议在规则中至少包含一个缩放设置”。 为此，请单击“添加规则”，并将“运算符”设置为“小于”，将“阈值”设置为“20”，将“操作”设置为“减少计数”。 现在应已创建一个可以根据 CPU 使用率进行扩展/缩减的缩放设置。
  ![基于 CPU 进行缩放][8]
- 单击“保存”

祝贺。 现在已成功创建第一个缩放设置，用于根据 CPU 使用率自动缩放 Web 应用。

> 注意：若要开始使用 VMSS 或云服务角色，也可以运用相同的步骤。

# <a name="other-considerations"></a>其他注意事项
## <a name="scale-based-on-a-schedule"></a>基于计划的缩放
除了始终根据 CPU 进行缩放以外，还可以设置为在特定的星期日期以不同的方式缩放。

- 单击“添加缩放条件”
- 设置缩放模式和规则的方式与设置默认条件相同
- 为计划选择“重复特定的星期日期”
- 选择星期日期，以及在选定的星期日期应该应用缩放条件的开始/结束时间

![基于计划的缩放条件][9]
## <a name="scale-differently-on-specific-dates"></a>在特定的日期以不同的方式缩放
除了始终根据 CPU 进行缩放以外，还可以设置为在特定的日期以不同的方式缩放。

- 单击“添加缩放条件”
- 设置缩放模式和规则的方式与设置默认条件相同
- 为计划选择“指定开始/结束日期”
- 选择开始/结束日期，以及在选定的日期应该应用缩放条件的开始/结束时间

![基于日期的缩放条件][10]

## <a name="view-the-scale-history-of-your-resource"></a>查看资源的缩放历史记录
每当扩展/缩减资源后，都会在活动日志中记录一个事件。 切换到“运行历史记录”选项卡即可查看资源在过去 24 小时的缩放历史记录。

![运行历史记录][11]

若要查看完整的缩放历史记录（最长 90 天），可以单击“单击此处查看更多详细信息”。 随后将启动活动日志，其中包含已预先选择“自动缩放”的资源和类别。

## <a name="view-the-scale-definition-of-the-resource"></a>查看资源的缩放定义
自动缩放设置是一种 ARM 资源。 切换到“JSON”选项卡即可在 JSON 中查看缩放定义。

![缩放定义][12]

如果需要，可以直接在 JSON 中进行更改。 保存后将反映这些更改。

## <a name="disable-autoscale-and-manually-scale-your-instances"></a>禁用自动缩放和手动缩放实例
有时，你可能想要禁用当前的缩放设置并手动缩放资源。

单击顶部的“禁用自动缩放”按钮。
![禁用自动缩放][13]

请注意，此选项会禁用配置，再次启用自动缩放后，仍可以继续使用这些配置。 现在，可以手动设置想要缩放到的实例数。

![设置手动缩放][14]

始终可以通过单击“启用自动缩放”，然后单击“保存”来恢复自动缩放。

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

