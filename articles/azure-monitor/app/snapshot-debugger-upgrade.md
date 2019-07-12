---
title: 对于.NET 应用的 azure Application Insights 快照调试器升级 |Microsoft Docs
description: 通过 Azure 应用服务或 Nuget 包将 Snapshot Debugger 升级到最新版本
services: application-insights
author: MarioHewardt
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: marioh
ms.reviewer: mbullwin
ms.openlocfilehash: 2dcf719e6c276c5641dc4c0040d8d7a808eeb3f2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706365"
---
# <a name="upgrading-the-snapshot-debugger"></a>升级 Snapshot Debugger

若要为你的数据提供可能的最佳安全性，Microsoft 正朝着远离 TLS 1.0 和 TLS 1.1，容易受到的攻击者已经证明。 如果使用旧版的站点扩展，则需要先升级，然后才能继续使用。 本文档概述了将 Snapshot Debugger 升级到最新版本所需执行的步骤。 可以使用两种主要的升级路径，具体取决于你是使用站点扩展启用了 Snapshot Debugger，还是使用了已添加到应用程序的 SDK/Nuget。 下面讨论这两种升级路径。 

## <a name="upgrading-the-site-extension"></a>升级站点扩展

如果使用站点扩展启用了 Snapshot Debugger，则可通过以下过程轻松进行升级：

1. 登录到 Azure 门户。
2. 导航到启用了 Application Insights 和 Snapshot Debugger 的资源。 例如，对于 Web 应用，请导航到应用服务资源：

   ![单个名为 DiagService01 的应用服务资源的屏幕截图](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 导航到资源以后，请单击“概览”边栏选项卡中的“Application Insights”：

   ![三个按钮的屏幕截图。 名为 Application Insights 的中心按钮已选中](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. 此时会打开一个新的边栏选项卡，其中包含当前设置。 如果不需要趁此机会更改设置，则可将设置保留原样。 边栏选项卡底部的“应用”按钮默认情况下未启用，若要激活该按钮，必须切换某个设置。  不需更改任何实际的设置，在更改此设置后，可以立即将其改回来。 建议切换 Profiler 设置，然后选择“应用”。 

   ![Application Insights 的“应用服务配置”页的屏幕截图，其中的“应用”按钮以红色突出显示](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. 单击“应用”以后，系统会要求你确认所做的更改。 

    > [!NOTE]
    > 在升级过程中，站点会重启。

   ![应用服务的应用监视提示的屏幕截图。 文本框显示消息：“我们现在会应用对应用设置的更改，并会安装将 Application Insights 资源链接到 Web 应用的工具。 此时会重启站点。 是否继续?”](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. 单击“是”，应用所做的更改。  在此过程中会显示一个通知，表明正在应用所做的更改：

   ![“应用更改”的屏幕截图 - 更新显示在右上角的扩展消息](./media/snapshot-debugger-upgrade/updating-extensions.png)

完成后，会显示“更改已应用”通知。 

   ![表明更改已应用的消息的屏幕截图](./media/snapshot-debugger-upgrade/changes-are-applied.png)

此站点现已升级，可以使用了。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>使用 SDK/Nuget 升级 Snapshot Debugger

如果应用程序使用低于 1.3.1 的 `Microsoft.ApplicationInsights.SnapshotCollector` 版本，则需将其升级到[更新的版本](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)，然后才能继续使用。
