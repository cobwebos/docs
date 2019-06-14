---
title: 对于.NET 应用的 azure Application Insights 快照调试器升级 |Microsoft Docs
description: 如何升级到 Azure 应用服务上或通过 Nuget 包的最新版本的快照调试器
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: Mario.Hewardt
ms.reviewer: mbullwin
ms.openlocfilehash: 54b79897ee378cda52106fe704e25c50a4325f38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60784124"
---
# <a name="upgrading-the-snapshot-debugger"></a>升级快照调试器

若要为你的数据提供可能的最佳安全性，Microsoft 正朝着远离 TLS 1.0 和 TLS 1.1，容易受到的攻击者已经证明。 如果使用的站点扩展的较旧版本，它将需要升级以继续工作。 本文档概述了升级到最新版本的快照调试程序所需的步骤。 如果启用了快照调试程序使用的站点扩展，或使用 SDK/Nuget 添加到你的应用程序有两个主要的升级路径，具体取决于。 下面将讨论这两个升级路径。 

## <a name="upgrading-the-site-extension"></a>升级站点扩展

如果您启用了快照调试程序使用站点扩展，你可以轻松升级使用以下过程：

1. 登录到 Azure 门户。
2. 导航到资源具有 Application Insights 和启用了快照调试程序。 例如，对于 Web 应用中，导航到应用服务资源：

   ![单个应用服务资源的屏幕截图名为 DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 一旦您导航到所需的资源，单击 Application Insights 概述边栏选项卡中：

   ![三个按钮的屏幕截图。 使用名称 Application Insights 的中心按钮处于选中状态](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. 使用当前设置将会打开新边栏选项卡。 除非你想要利用此机会来更改设置，您可以将其按原样保留。 **应用**边栏选项卡的底部按钮未启用默认情况下，必须切换一个要激活该按钮的设置。 无需更改任何实际的设置，而不是您可以更改该设置，然后立即返回更改。 我们建议切换 Profiler 设置，并选择**应用**。

   ![使用以红色突出显示应用按钮的屏幕截图的 Application Insights 应用服务配置页](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. 单击后**应用**，您将需要确认所做的更改。

    > [!NOTE]
    > 升级过程的一部分，将重新启动站点。

   ![屏幕截图的应用服务的应用监视提示符。 文本框将显示消息："我们现在将更改应用到你的应用设置，并安装工具，以便链接到 web 应用的 Application Insights 资源。 这将重新启动站点。 是否继续?”](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. 单击**是**以应用更改。 在过程中，将显示一条通知，显示正在应用更改：

   ![将更改应用的更新显示在右上角中的扩展消息的屏幕截图](./media/snapshot-debugger-upgrade/updating-extensions.png)

完成后， **"应用更改"** 会出现通知。

   ![应用的消息，指出更改屏幕截图](./media/snapshot-debugger-upgrade/changes-are-applied.png)

站点现已升级，并且可供使用。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>升级使用 SDK/Nuget 的快照调试器

如果应用程序使用的版本`Microsoft.ApplicationInsights.SnapshotCollector`低于版本 1.3.1，它将需要升级到[较新版本](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)来继续处理。
