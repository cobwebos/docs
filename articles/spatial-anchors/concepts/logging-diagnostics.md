---
title: 日志记录和诊断 Azure 空间的定位点在 |Microsoft Docs
description: 如何生成和检索日志记录和诊断 Azure 空间的定位点中的深入的说明。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 134023c0884ce3a402b99806f1bf19dcb59ecc32
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57882822"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>日志记录和诊断在 Azure 空间的定位点

Azure 空间的定位点提供对应用程序开发非常有用的标准日志记录机制。 空间的定位点诊断日志记录模式时，你需要进行调试的详细信息。 诊断日志记录存储在环境的映像。

## <a name="standard-logging"></a>标准日志记录
在空间的定位点 API 中，您可以订阅以获取应用程序开发和调试的有用的日志的日志记录机制。 标准日志记录 Api 不要设备磁盘上存储的环境的图片。 SDK 将作为事件的回调提供这些日志。 由你来将这些日志集成到应用程序的日志记录机制。

### <a name="configuration-of-log-messages"></a>日志消息的配置
有两个用户感兴趣的回调。 下面的示例演示如何配置会话。

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>事件和属性

提供这些事件的回调来处理日志和会话中的错误：

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel):指定要从运行时接收的事件的详细信息的级别。
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug):提供标准调试日志事件。
- [错误](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)：提供运行时将其视为错误的日志事件。

## <a name="diagnostics-logging"></a>诊断日志记录

除了日志记录的操作的标准模式下，空间的定位点也具有诊断模式。 诊断模式中捕获环境的映像并将其记录到磁盘。 此模式可用于调试某些类型的问题，如未能以可预测方式找到一个定位点。 启用诊断日志记录仅用于重现特定问题。 然后将其禁用。 通常情况下运行您的应用程序时，不要启用诊断。

与 Microsoft 支持交互，期间 Microsoft 代表可能会问你是否要将提交诊断捆绑包，以便进一步进行调查。 在这种情况下，您可能决定启用诊断并重现此问题，因此你可以提交诊断捆绑包。 

如果在诊断日志到 Microsoft 没有提交之前确认收到 Microsoft 代表，提交将获得答复。

以下部分说明如何启用诊断模式以及如何将提交给 Microsoft 的诊断日志。

### <a name="enable-diagnostics-logging"></a>启用诊断日志记录

启用诊断日志记录的会话，会话中的所有操作都有相应的诊断日志记录本地文件系统中。 记录期间，环境将保存到磁盘。

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>提交诊断捆绑包

下面的代码段演示如何提交给 Microsoft 的诊断捆绑。 此捆绑包将包含由会话后启用诊断捕获的环境的映像。 

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>诊断捆绑包的部分
诊断捆绑包可能包含以下信息：

- **关键帧图像**:诊断已启用时，在会话期间捕获的环境的映像。
- **日志**：日志记录的运行时事件。
- **会话元数据**:标识的会话的元数据。
