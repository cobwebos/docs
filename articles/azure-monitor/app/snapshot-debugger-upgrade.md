---
title: .NET 应用 Azure 应用程序 Insights Snapshot Debugger 升级 |Microsoft Docs
description: 如何将 Snapshot Debugger 升级到 Azure 应用 Services 上的最新版本，或升级到 Nuget 包
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899834"
---
# <a name="upgrading-the-snapshot-debugger"></a>升级 Snapshot Debugger

为了为你的数据提供尽可能最佳的安全性，Microsoft 正远离 TLS 1.0 和 TLS 1.1，因为它已被认为容易受到确定的攻击者的攻击。 如果你使用的是较旧版本的站点扩展，则需要升级才能继续工作。 本文档概述了将快照调试器升级到最新版本所需的步骤。 有两个主要升级路径，具体取决于是否已使用站点扩展启用了 Snapshot Debugger，或者是否使用了已添加到应用程序的 SDK/Nuget。 下面讨论了这两种升级途径。 

## <a name="upgrading-the-site-extension"></a>升级站点扩展

如果使用站点扩展启用了快照调试器，则可以使用以下过程轻松升级：

1. 登录到 Azure 门户。
2. 导航到已启用 Application Insights 和快照调试器的资源。 例如，对于 Web 应用，请导航到 "应用服务" 资源：

   ![名为 DiagService01 的单个应用服务资源的屏幕截图](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 导航到资源后，请单击 "概述" 边栏选项卡中 Application Insights：

   ![三个按钮的屏幕截图。 选择了名称 Application Insights 的中心按钮](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. 随即打开一个新的边栏选项卡，其中包含当前设置。 除非您想要更改您的设置，否则您可以将其保留原样。 默认情况下，边栏选项卡底部的 "**应用**" 按钮未启用，你必须切换其中一个设置来激活该按钮。 无需更改任何实际设置，而可以更改设置，然后立即将其更改。 建议切换探查器设置，然后选择 "**应用**"。

   ![使用红色突出显示 "应用" 按钮 Application Insights 应用服务配置页的屏幕截图](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. 单击 "**应用**" 后，系统将要求你确认所做的更改。

    > [!NOTE]
    > 在升级过程中，将重新启动站点。

   ![应用服务的应用监视提示屏幕截图。 文本框显示消息： "我们现在会将更改应用到应用设置，并安装工具以将 Application Insights 资源链接到 web 应用。 这将重新启动站点。 是否继续?”](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. 单击 **"是"** 以应用更改。 在此过程中，将显示一条通知，显示正在应用更改：

   ![显示在右上角的 "应用更改-正在更新扩展" 消息的屏幕截图](./media/snapshot-debugger-upgrade/updating-extensions.png)

完成后，将显示 **"应用更改"** 通知。

   ![说明应用更改的消息的屏幕截图](./media/snapshot-debugger-upgrade/changes-are-applied.png)

该网站现已升级，并且已准备就绪，可供使用。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>使用 SDK/Nuget 升级 Snapshot Debugger

如果应用程序使用1.3.1 以下版本的 `Microsoft.ApplicationInsights.SnapshotCollector`，则需要将其升级到[较新的版本](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)以继续工作。
