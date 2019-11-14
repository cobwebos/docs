---
title: 语音 SDK 日志记录 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何在 Speech SDK 中启用日志记录（C++、 C#、Python、目标-C、Java）。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 58f6c97ac819947f84735bc0bc4c125b43db58dc
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075802"
---
# <a name="enable-logging-in-the-speech-sdk"></a>在语音 SDK 中启用日志记录

将日志记录到文件是语音 SDK 的一项可选功能。 在开发期间，日志记录可以提供语音 SDK 核心组件的附加信息和诊断数据。 将语音配置对象中的属性 `Speech_LogFilename` 设置为日志文件的位置和名称可以启用日志记录。 基于该配置创建识别器后，将会全局激活日志记录，以后无法将其禁用。 在运行日志记录会话期间，无法更改日志文件的名称。

> [!NOTE]
> 从语音 SDK 版本 1.4.0 开始，日志记录可在所有支持的语音 SDK 编程语言中使用，但 JavaScript 除外。

## <a name="sample"></a>示例

日志文件名在配置对象中指定。 以 `SpeechConfig` 为例，假设你已创建名为 `config` 的实例：

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

可以基于配置对象创建识别器。 这会为所有识别器启用日志记录。

> [!NOTE]
> 如果基于配置对象创建 `SpeechSynthesizer`，则不会启用日志记录。 不过，如果启用了日志记录，则还会从 `SpeechSynthesizer` 收到诊断数据。

## <a name="create-a-log-file-on-different-platforms"></a>在不同的平台上创建日志文件

对于 Windows 或 Linux，日志文件可以位于用户有权写入的任何路径。 默认情况下，对其他操作系统中的文件系统位置的写入权限可能会受到限制。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

UWP 应用程序需将日志文件放在某个应用程序数据位置（本地、漫游或临时位置）。 可在本地应用程序文件夹中创建日志文件：

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

[此处](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)提供了有关 UWP 应用程序的文件访问权限的详细信息。

### <a name="android"></a>Android

可将日志文件保存到内部存储、外部存储或缓存目录。 在内部存储或缓存目录中创建的文件专用于应用程序。 最好是在外部存储中创建日志文件。

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

以上代码将日志文件保存到应用程序特定的目录的根目录中的外部存储。 用户可以使用文件管理器访问该文件（通常位于 `Android/data/ApplicationName/logfile.txt`）。 卸载应用程序时，会删除该文件。

还需要请求清单文件中的 `WRITE_EXTERNAL_STORAGE` 权限：

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

[此处](https://developer.android.com/guide/topics/data/data-storage.html)提供了有关适用于 Android 应用程序的数据和文件存储的详细信息。

#### <a name="ios"></a>iOS

只能访问应用程序沙盒中的目录。 可以在文档、库和临时目录中创建文件。 文档目录中的文件可供用户使用。 以下代码片段演示如何在应用程序文档目录中创建日志文件：

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

若要访问创建的文件，请将以下属性添加到应用程序的 `Info.plist` 属性列表中：

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

[此处](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)提供了有关 iOS 文件系统的详细信息。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的示例](https://aka.ms/csspeech/samples)
