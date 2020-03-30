---
title: 升级 Azure Application Insights Snapshot Debugger
description: 如何在 Azure 应用服务上或通过 Nuget 包将 Snapshot Debugger for .NET 应用升级到最新版本
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671386"
---
# <a name="upgrading-the-snapshot-debugger"></a>升级 Snapshot Debugger

为了尽可能确保数据安全，Microsoft 正在停用 TLS 1.0 和 TLS 1.1，这两个版本已证明容易受到确定的攻击者的攻击。 如果使用旧版的站点扩展，则需要先升级，然后才能继续使用。 本文档概述了将 Snapshot Debugger 升级到最新版本所需执行的步骤。 可以使用两种主要的升级路径，具体取决于你是使用站点扩展启用了 Snapshot Debugger，还是使用了已添加到应用程序的 SDK/Nuget。 下面讨论这两种升级路径。 

## <a name="upgrading-the-site-extension"></a>升级站点扩展

> [!IMPORTANT]
> 旧版 Application Insights 使用名为“Azure 应用服务的 Application Insights 扩展”的专用站点扩展。__ 启用当前 Application Insights 体验的方法是：设置“应用设置”以启用预装的站点扩展。
> 为了避免冲突（冲突可能会导致站点停止运行），必须先删除专用站点扩展。 请参阅下面的步骤 4。

如果使用站点扩展启用了 Snapshot Debugger，则可通过以下过程进行升级：

1. 登录到 Azure 门户。  
2. 导航到启用了 Application Insights 和 Snapshot Debugger 的资源。 例如，对于 Web 应用，请导航到应用服务资源：

   ![单个名为 DiagService01 的应用服务资源的屏幕截图](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 导航到资源以后，请单击“扩展”边栏选项卡，等待扩展列表填充：

   ![应用服务扩展的屏幕截图，显示 Azure 应用服务的 Application Insights 扩展已安装](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. 如果安装了 Azure 应用服务的 Application Insights 扩展的任何版本，请将其选中并单击“删除”。__ 确认“是”以删除该扩展并等待删除操作完成，然后转到下一步。****

   ![应用服务扩展的屏幕截图，显示 Azure 应用服务的 Application Insights 扩展，并突出显示了“删除”按钮](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. 转到资源的“概览”边栏选项卡，单击“Application Insights”：

   ![三个按钮的屏幕截图。 名为 Application Insights 的中心按钮已选中](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. 如果这是首次查看此应用服务的 Application Insights 边栏选项卡，系统会提示启用 Application Insights。 选择“启用 Application Insights”。****
 
   ![首次体验 Application Insights 边栏选项卡的屏幕截图，突出显示了“启用 Application Insights”按钮](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. 显示了当前的 Application Insights 设置。 如果不需要趁此机会更改设置，则可将设置保留原样。 边栏选项卡底部的“应用”按钮默认情况下未启用，若要激活该按钮，必须切换某个设置。**** 不需更改任何实际的设置，在更改此设置后，可以立即将其改回来。 建议切换 Profiler 设置，然后选择“应用”。****

   ![Application Insights 的“应用服务配置”页的屏幕截图，其中的“应用”按钮以红色突出显示](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. 单击“应用”以后，系统会要求你确认所做的更改。****

    > [!NOTE]
    > 在升级过程中，站点会重启。

   ![应用服务的应用监视提示的屏幕截图。 文本框显示消息："我们现在将更改应用于您的应用设置，并安装我们的工具，将您的应用程序见解资源链接到 Web 应用。 此时会重启站点。 是否继续?”](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. 单击“是”应用所做的更改，然后等待该过程完成。****

此站点现已升级，可以使用了。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>使用 SDK/Nuget 升级 Snapshot Debugger

如果应用程序使用低于 1.3.1 的 `Microsoft.ApplicationInsights.SnapshotCollector` 版本，则需将其升级到[更新的版本](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)，然后才能继续使用。
