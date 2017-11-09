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
ms.date: 07/07/2017
ms.author: rajram
ms.openlocfilehash: 84ce3af2bef1e3204ad91567d155d7cbf90e9c7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-autoscale-in-azure"></a>Azure 中的自动缩放入门
本文介绍如何在 Microsoft Azure 门户中为资源指定自动缩放设置。

Azure Monitor 自动缩放功能仅适用于虚拟机规模集、云服务、Azure 应用服务计划和应用服务环境。 

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>了解订阅中的自动缩放设置
可在 Azure Monitor 中查找自动缩放功能适用的所有资源。 按下列步骤进行分步演练：

1. 打开 [Azure 门户。][1]
2. 单击左窗格中的“Azure Monitor”图标。
  ![打开 Azure Monitor][2]
3. 单击“自动缩放”以查看自动缩放适用的所有资源及其当前的自动缩放状态。
  ![了解 Azure Monitor 中的自动缩放功能][3]

可使用顶部的筛选器窗格缩小列表的范围，以选择特定资源组中的资源、特定的资源类型或特定资源。

对于每个资源，将会看到其当前实例计数和自动缩放状态。 自动缩放状态可以是：

- 未配置：尚未对此资源启用自动缩放功能。
- 已启用：已对此资源启用自动缩放功能。
- 已禁用：已对此资源禁用自动缩放功能。

## <a name="create-your-first-autoscale-setting"></a>创建第一个自动缩放设置

现在，让我们完成一个简单的分步演练，以创建第一个自动缩放设置。

1. 在 Azure Monitor 中打开“自动缩放”边栏选项卡，然后选择要缩放的资源。 （以下步骤使用与某 Web 应用关联的应用服务计划。 [仅需 5 分钟，就可在 Azure 中创建首个 ASP.NET Web 应用。][4]）
2. 请注意当前实例计数为 1。 单击“启用自动缩放”。
  ![新 Web 应用的缩放设置][5]
3. 提供缩放设置的名称，然后单击“添加规则”。 请注意右侧以上下文窗格形式打开的缩放规则选项。 默认情况下，这将选项设置为当资源的 CPU 百分比超过 70% 时，将实例计数缩放 1 个单位。 请将此选项保留默认值，并单击“添加”。
  ![为 Web 应用创建缩放设置][6]
4. 现已创建第一个缩放规则。 请注意，UX 建议了最佳做法，并指出“建议至少在规则中包含一个缩放设置”。 为此，请执行以下操作：
  
    a. 单击“添加规则”。 

    b. 将“运算符”设置为“小于”。

    c. 将“阈值”设置为 20。

    d.单击“下一步”。 将“操作”设置为“按以下值递减计数”。

   现在应已创建一个可以根据 CPU 使用率进行扩展/缩减的缩放设置。
   ![基于 CPU 进行缩放][8]
5. 单击“保存” 。

祝贺你！ 现已成功创建第一个缩放设置，用于根据 CPU 使用率自动缩放 Web 应用。

> [!NOTE] 
> 若要开始使用虚拟机规模集或云服务角色，才可采用相同步骤操作。

## <a name="other-considerations"></a>其他注意事项
### <a name="scale-based-on-a-schedule"></a>基于计划的缩放
除了基于 CPU 进行缩放，还可设置为在特定的星期日期按其他方式缩放。

1. 单击“添加缩放条件”。
2. 缩放模式和规则的设置方式与默认条件的相同。
3. 为计划选择“重复特定的星期日期”。
4. 选择星期日期，以及需应用缩放条件的开始/结束时间。

![基于计划的缩放条件][9]
### <a name="scale-differently-on-specific-dates"></a>在特定的日期以不同的方式缩放
除了基于 CPU 进行缩放，还可设置为在特定日期按其他方式缩放。

1. 单击“添加缩放条件”。
2. 缩放模式和规则的设置方式与默认条件的相同。
3. 为计划选择“指定开始/结束日期”。
4. 选择开始/结束日期，以及需应用缩放条件的开始/结束时间。

![基于日期的缩放条件][10]

### <a name="view-the-scale-history-of-your-resource"></a>查看资源的缩放历史记录
每次增加或缩小资源后，都会在活动日志中记录一个事件。 切换到“运行历史记录”选项卡即可查看资源在过去 24 小时的缩放历史记录。

![运行历史记录][11]

若要查看完整的缩放历史记录（最长 90 天），请选择“单击此处查看更多详细信息”。 随后将启动活动日志，其中包含已预先选择“自动缩放”的资源和类别。

### <a name="view-the-scale-definition-of-your-resource"></a>查看资源的缩放定义
“自动缩放”是一种 Azure 资源管理器资源。 切换到“JSON”选项卡即可在 JSON 中查看缩放定义。

![缩放定义][12]

如果需要，可以直接在 JSON 中进行更改。 这些更改将在保存后生效。

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>禁用“自动缩放”并手动缩放实例
有时，可能需要禁用当前的缩放设置并手动缩放资源。

单击顶部的“禁用自动缩放”按钮。
![禁用自动缩放][13]

> [!NOTE] 
> 此选项将禁用你的配置。 但是，再次启用“自动缩放”后，则可恢复此设置。 

现在，可手动设置要缩放到的实例数。

![设置手动缩放][14]

始终可单击“启用自动缩放”，再单击“保存”来恢复自动缩放。

## <a name="next-steps"></a>后续步骤
- [创建活动日志警报以监视订阅上的所有自动缩放引擎操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [创建活动日志警报以监视订阅上所有失败的自动缩放缩小/扩大操作](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
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

