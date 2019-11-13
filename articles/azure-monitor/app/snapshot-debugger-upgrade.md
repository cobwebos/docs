---
title: .NET 应用 Azure 应用程序 Insights Snapshot Debugger 升级 |Microsoft Docs
description: 通过 Azure 应用服务或 Nuget 包将 Snapshot Debugger 升级到最新版本
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 51642dde3de16f2bed3ca247e573237effb30917
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73936035"
---
# <a name="upgrading-the-snapshot-debugger"></a>升级 Snapshot Debugger

为了为你的数据提供尽可能最佳的安全性，Microsoft 正远离 TLS 1.0 和 TLS 1.1，因为它已被认为容易受到确定的攻击者的攻击。 如果你使用的是较旧版本的站点扩展，则需要升级才能继续工作。 本文档概述了将 Snapshot Debugger 升级到最新版本所需执行的步骤。 可以使用两种主要的升级路径，具体取决于你是使用站点扩展启用了 Snapshot Debugger，还是使用了已添加到应用程序的 SDK/Nuget。 下面讨论这两种升级路径。 

## <a name="upgrading-the-site-extension"></a>升级站点扩展

> [!IMPORTANT]
> 旧版本的 Application Insights 使用名_为 Application Insights 扩展的_专用站点扩展 Azure App Service。 通过将应用设置设置为亮起预安装的站点扩展来启用当前 Application Insights 体验。
> 若要避免冲突（这可能会导致站点停止工作），请首先删除专用站点扩展，这一点很重要。 请参阅下面的步骤4。

如果使用站点扩展启用了快照调试器，则可以使用以下过程进行升级：

1. 登录到 Azure 门户。
2. 导航到启用了 Application Insights 和 Snapshot Debugger 的资源。 例如，对于 Web 应用，请导航到应用服务资源：

   ![单个名为 DiagService01 的应用服务资源的屏幕截图](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 导航到资源后，请单击 "扩展" 边栏选项卡，并等待要填充的扩展列表：

   ![显示已安装 Azure App Service Application Insights 扩展的应用服务扩展的屏幕截图](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. 如果安装了_Azure App Service 的 Application Insights 扩展_的任何版本，请将其选中，然后单击 "删除"。 确认 **"是"** 删除扩展并等待删除完成，然后再转到下一步。

   ![应用服务扩展的屏幕截图，其中突出显示了 "删除" 按钮 Azure App Service Application Insights 扩展](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. 请参阅资源的 "概述" 边栏选项卡，并单击 Application Insights：

   ![三个按钮的屏幕截图。 名为 Application Insights 的中心按钮已选中](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. 如果这是你第一次查看此应用服务的 Application Insights 边栏选项卡，系统会提示你启用 Application Insights。 选择 **"打开 Application Insights"** 。
 
   ![突出显示 "打开 Application Insights" 按钮的 "Application Insights" 边栏选项卡首次体验的屏幕截图](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. 将显示当前 Application Insights 设置。 如果不需要趁此机会更改设置，则可将设置保留原样。 默认情况下，边栏选项卡底部的 "**应用**" 按钮未启用，你必须切换其中一个设置来激活该按钮。 不需更改任何实际的设置，在更改此设置后，可以立即将其改回来。 建议切换 Profiler 设置，然后选择“应用”。

   ![Application Insights 的“应用服务配置”页的屏幕截图，其中的“应用”按钮以红色突出显示](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. 单击 "**应用**" 后，系统将要求你确认所做的更改。

    > [!NOTE]
    > 在升级过程中，站点会重启。

   ![应用服务的应用监视提示的屏幕截图。 文本框显示消息： "我们现在会将更改应用到应用设置，并安装工具以将 Application Insights 资源链接到 web 应用。 此时会重启站点。 是否继续?”](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. 单击 **"是"** 以应用更改并等待进程完成。

此站点现已升级，可以使用了。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>使用 SDK/Nuget 升级 Snapshot Debugger

如果应用程序使用低于 1.3.1 的 `Microsoft.ApplicationInsights.SnapshotCollector` 版本，则需将其升级到[更新的版本](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)，然后才能继续使用。
