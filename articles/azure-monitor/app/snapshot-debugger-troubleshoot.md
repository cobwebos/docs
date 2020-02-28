---
title: 排查 Azure 应用程序 Insights Snapshot Debugger
description: 本文介绍了疑难解答步骤和信息，以帮助在启用或使用 Application Insights Snapshot Debugger 时遇到问题的开发人员。
ms.topic: conceptual
author: brahmnes
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 485f35ed249ab7f6bbb987d8c79afe20287cd25a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671403"
---
# <a id="troubleshooting"></a>排查 Application Insights Snapshot Debugger 或查看快照时的问题
如果为应用程序启用了 Application Insights Snapshot Debugger，但没有看到用于例外的快照，则可以使用这些说明进行故障排除。 可能有许多不同的原因导致不生成快照。 可以运行快照运行状况检查来确定可能的一些常见原因。

## <a name="use-the-snapshot-health-check"></a>使用快照运行状况检查
几个常见问题会导致不显示“打开调试快照”。 例如，使用过时的快照收集器；达到每日上传限制；或者可能快照只是需要很长时间上传。 使用“快照运行状况检查”解决常见问题。

端到端跟踪视图的异常窗格中有一个链接，可将你带到“快照运行状况检查”。

![进入“快照运行状况检查”](./media/snapshot-debugger/enter-snapshot-health-check.png)

这个交互式类似聊天的界面可查找常见问题并指导你解决问题。

![运行状况检查](./media/snapshot-debugger/healthcheck.png)

如果这样不能解决问题，请参阅以下手动故障排除步骤。

## <a name="verify-the-instrumentation-key"></a>验证检测密钥

请确保在发布的应用程序中使用正确的检测密钥。 通常，从 ApplicationInsights.config 文件中读取检测密钥。 请验证该值是否与在门户中看到的 Application Insights 资源的检测密钥相同。

## <a name="preview-versions-of-net-core"></a>.NET Core 的预览版本
如果应用程序使用 .NET Core 的预览版本，并且通过门户中的 " [Application Insights" 窗格](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)启用了 Snapshot Debugger，则 Snapshot Debugger 可能无法启动。 按照 "[启用其他环境的 Snapshot Debugger](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) " 中的说明操作，[将 microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包***添加***到应用程序，并通过 " [Application Insights" 窗格](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)启用。


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>升级到最新版本的 NuGet 包

如果 Snapshot Debugger 是通过[门户中](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)的 "Application Insights" 窗格启用的，则应用程序应已在运行最新的 NuGet 包。 如果通过包括[Applicationinsights.config Microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nuget 包启用了 Snapshot Debugger，请使用 Visual Studio 的 Nuget 包管理器，以确保使用最新版本的 applicationinsights.config microsoft.applicationinsights.snapshotcollector。 可以在 https://github.com/Microsoft/ApplicationInsights-Home/issues/167 中找到发行说明

## <a name="check-the-uploader-logs"></a>检查上传程序日志

创建快照后，将在磁盘上创建一个小型转储文件 (.dmp)。 一个单独的上传程序进程会创建该小型转储文件，并将其连同所有关联的 PDB 一起上传到 Application Insights Snapshot Debugger 存储。 成功上传小型转储后，会将其从磁盘中删除。 上传程序进程的日志文件会保留在磁盘上。 在应用服务环境中，可在 `D:\Home\LogFiles` 中找到这些日志。 通过应用服务的 Kudu 管理站点查找这些日志文件。

1. 在 Azure 门户中，打开应用服务应用程序。
2. 单击“高级工具”，或搜索 **Kudu**。
3. 单击“开始”。
4. 在“调试控制台”下拉列表框中，选择“CMD”。
5. 单击“日志文件”。

应至少看到一个名称以 `Uploader_` 或 `SnapshotUploader_` 开头，且扩展名为 `.log` 的文件。 单击相应图标，下载任意日志文件或在浏览器中打开文件。
文件名包括可标识应用服务实例的唯一后缀。 如果应用服务实例托管于多台计算机上，则每台计算机都有单独的日志文件。 当上传程序检测到新的小型转储文件时，会将其记录在日志文件中。 下面是成功的快照和上传的示例：

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> 上面的示例来自 1.2.0 版的 Microsoft.ApplicationInsights.SnapshotCollector NuGet 包。 在更早的版本中，上传程序进程名为 `MinidumpUploader.exe`，且日志不太详细。

在之前的示例中，检测密钥是 `c12a605e73c44346a984e00000000000`。 此值应与应用程序的检测密钥相匹配。
小型转储与 ID 为 `139e411a23934dc0b9ea08a626db16c5` 的快照相关联。 稍后可以使用此 ID 在 Application Insights Analytics 中找到关联的异常遥测。

上传程序大约每 15 分钟扫描一次新 PDB。 下面是一个示例：

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

对于未托管于应用服务中的应用程序，上传程序日志与小型转储位于同一文件夹：`%TEMP%\Dumps\<ikey>`（其中 `<ikey>` 是检测密钥）。

## <a name="troubleshooting-cloud-services"></a>云服务故障排除
对于云服务中的角色而言，默认临时文件夹可能太小，无法容纳小型转储文件，从而导致丢失快照。
所需的空间取决于应用程序的总工作集量和并发快照数。
32 位 ASP.NET Web 角色的工作集通常在 200 MB 到 500 MB 之间。
允许存在至少两个并发快照。
例如，如果应用程序使用 1 GB 的总工作集，则应确保是否至少有 2 GB 的磁盘空间可用来存储快照。
按照这些步骤为云服务角色配置快照的专用本地资源。

1. 通过编辑云服务定义 (.csdef) 文件将新的本地资源添加到云服务中。 以下示例使用 5 GB 大小定义名为 `SnapshotStore` 的资源。
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. 修改角色的启动代码以添加指向 `SnapshotStore` 本地资源的环境变量。 对于辅助角色，应将代码添加到角色的 `OnStart` 方法：
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   对于 Web 角色 (ASP.NET)，应将代码添加到 Web 应用程序的 `Application_Start` 方 法：
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. 更新角色的 ApplicationInsights.config 文件以重写 `SnapshotCollector` 使用的临时文件夹位置
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>重写影子副本文件夹

当快照收集器启动时，它会尝试在磁盘上查找适合用于运行快照上传程序进程的文件夹。 所选的文件夹称为影子副本文件夹。

快照收集器检查几个已知位置，并确保它有权复制快照上传程序二进制文件。 使用了以下环境变量：
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

如果找不到合适的文件夹，则快照收集器将报告一个错误，指出“找不到合适的影子副本文件夹。”

如果复制失败，则快照收集器会报告一个 `ShadowCopyFailed` 错误。

如果无法启动上传程序，则快照收集器会报告一个 `UploaderCannotStartFromShadowCopy` 错误。 消息的正文通常包含 `System.UnauthorizedAccessException`。 发生此错误通常是因为应用程序正在权限降低的帐户下运行。 此帐户有权向影子副本文件夹进行写入，但无权执行代码。

因为这些错误通常发生在启动期间，所以它们后面通常会跟有一个 `ExceptionDuringConnect` 错误，指出“上传程序无法启动。”

若要解决这些错误，可以通过 `ShadowCopyFolder` 配置选项手动指定影子副本文件夹。 例如，使用 ApplicationInsights.config：

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

或者，如果将 appsettings.json 用于 .NET Core 应用程序，则使用：

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>使用 Application Insights 搜索查找附带快照的异常

创建快照后，出现的异常标记有快照 ID。 向 Application Insights 报告异常遥测时，该快照 ID 作为自定义属性包含在内。 通过 Application Insights 中的“搜索”，可借助 `ai.snapshot.id` 自定义属性找到所有遥测。

1. 在 Azure 门户中浏览到 Application Insights 资源。
2. 单击 **“搜索”** 。
3. 在“搜索”文本框中输入 `ai.snapshot.id`，然后按 Enter。

![在门户中使用快照 ID 搜索遥测](./media/snapshot-debugger/search-snapshot-portal.png)

如果此搜索未返回任何结果，则表示在选定的时间范围内，未向 Application Insights 报告任何有关你的应用程序的快照。

若要从上传程序日志中搜索特定快照 ID，请在“搜索”框中键入该 ID。 如果快照已上传但找不到该快照的遥测，请按照以下步骤操作：

1. 请验证检测密钥，仔细检查正在查看的 Application Insights 资源是否正确。

2. 使用上传程序日志中的时间戳调整搜索的“时间范围”筛选器，以包含该时间范围。

如果仍然看不到任何带快照 ID 的异常，则表示未向 Application Insights 报告该异常遥测。 如果应用程序在拍摄快照后，但还未报告异常遥测就崩溃，则会发生这种情况。 在这种情况下，请查看 `Diagnose and solve problems` 下的应用服务日志，了解是否存在意外重启或未处理的异常。

## <a name="edit-network-proxy-or-firewall-rules"></a>编辑网络代理或防火墙规则

如果应用程序通过代理或防火墙连接到 Internet，则可能需要编辑规则以允许应用程序与 Snapshot Debugger 服务进行通信。 Snapshot Debugger 使用的 Ip 包含在 Azure Monitor 服务标记中。
