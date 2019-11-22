---
title: 日志记录和诊断
description: 详细说明如何在 Azure 空间锚中生成和检索日志记录和诊断。
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: f4359db1deda2295a66bcb97cf374d0fe9bc3ef7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74270129"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Azure 空间锚中的日志记录和诊断

Azure 空间锚提供标准的日志记录机制，适用于应用程序开发。 如果需要详细信息来进行调试，空间锚记诊断日志记录模式非常有用。 诊断日志记录存储环境的映像。

## <a name="standard-logging"></a>标准日志记录
在空间锚点 API 中，你可以订阅日志记录机制以获取用于应用程序开发和调试的有用日志。 标准日志记录 Api 不会在设备磁盘上存储环境的图片。 SDK 将这些日志作为事件回调提供。 将这些日志集成到应用程序的日志记录机制。

### <a name="configuration-of-log-messages"></a>日志消息配置
用户有两个兴趣的回叫。 下面的示例演示如何配置会话。

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

提供这些事件回调是为了处理来自会话的日志和错误：

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel)：指定要从运行时接收的事件的详细级别。
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug)：提供标准调试日志事件。
- [错误](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)：提供运行时视为错误的日志事件。

## <a name="diagnostics-logging"></a>诊断日志记录

除了用于日志记录的标准操作模式，空间定位点还具有诊断模式。 诊断模式捕获环境的映像，并将其记录到磁盘。 您可以使用此模式来调试某些类型的问题，如无法预测的定位定位点。 仅启用诊断日志记录以重现特定问题。 然后将其禁用。 请不要在正常运行应用时启用诊断。

在与 Microsoft 交互时，Microsoft 代表可能会询问你是否愿意提交诊断捆绑以便进一步调查。 在这种情况下，您可能决定启用诊断并重现问题，以便您可以提交诊断捆绑包。

如果在未事先确认 Microsoft 代表的情况下将诊断日志提交给 Microsoft，则提交将无应答。

以下部分介绍了如何启用诊断模式，以及如何将诊断日志提交给 Microsoft。

### <a name="enable-diagnostics-logging"></a>启用诊断日志记录

启用诊断日志记录会话时，该会话中的所有操作都在本地文件系统中具有相应的诊断日志记录。 在日志记录过程中，会将环境的映像保存到磁盘。

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

以下代码片段演示了如何将诊断捆绑提交给 Microsoft。 此绑定将包括启用诊断后会话捕获的环境的映像。

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

### <a name="parts-of-a-diagnostics-bundle"></a>诊断捆绑包的部件
诊断捆绑包可能包含以下信息：

- **关键帧映像**：启用诊断时在会话过程中捕获的环境图像。
- **日志**：运行时记录的日志事件。
- **会话元数据**：标识会话的元数据。
