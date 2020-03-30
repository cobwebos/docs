---
title: 日志记录和诊断
description: 有关如何在 Azure 空间锚点中生成和检索日志记录和诊断的深入说明。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270129"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Azure 空间锚点中的日志记录和诊断

Azure 空间锚点提供了一个标准日志记录机制，该机制对应用开发很有用。 当您需要更多信息进行调试时，空间锚点诊断日志记录模式非常有用。 诊断日志记录存储环境映像。

## <a name="standard-logging"></a>标准日志记录
在空间锚点 API 中，您可以订阅日志记录机制，以获得有用的日志来进行应用程序开发和调试。 标准日志记录 API 不会将环境图片存储在设备磁盘上。 SDK 将这些日志作为事件回调提供。 您要将这些日志集成到应用程序的日志记录机制中。

### <a name="configuration-of-log-messages"></a>日志消息的配置
用户有两个感兴趣的回调。 下面的示例演示如何配置会话。

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

提供这些事件回调来处理会话中的日志和错误：

- [日志级别](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel)：指定从运行时接收的事件的详细级别。
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug)：提供标准的调试日志事件。
- [错误](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)： 提供运行时认为是错误的日志事件。

## <a name="diagnostics-logging"></a>诊断日志记录

除了日志记录的标准操作模式外，空间锚点还具有诊断模式。 诊断模式捕获环境映像并将其记录到磁盘。 您可以使用此模式调试某些类型的问题，例如无法预测地找到锚点。 启用诊断日志记录以仅重现特定问题。 然后禁用它。 正常运行应用时，不要启用诊断。

在与 Microsoft 进行支持交互时，Microsoft 代表可能会询问您是否愿意提交诊断包以供进一步调查。 在这种情况下，您可以决定启用诊断并重现问题，以便提交诊断包。

如果您在未事先获得 Microsoft 代表的确认的情况下向 Microsoft 提交诊断日志，则提交将得不到答复。

以下各节演示如何启用诊断模式以及如何向 Microsoft 提交诊断日志。

### <a name="enable-diagnostics-logging"></a>启用诊断日志记录

启用诊断日志记录会话时，会话中的所有操作在本地文件系统中都有相应的诊断日志记录。 在日志记录期间，环境映像将保存到磁盘。

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

### <a name="submit-the-diagnostics-bundle"></a>提交诊断包

以下代码段演示如何向 Microsoft 提交诊断包。 此捆绑包将包括启用诊断后会话捕获的环境映像。

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

### <a name="parts-of-a-diagnostics-bundle"></a>诊断包的部件
诊断包可能包含以下信息：

- **关键帧图像**：启用诊断时会话期间捕获的环境图像。
- **日志**：记录运行时记录的事件。
- **会话元数据**：标识会话的元数据。
