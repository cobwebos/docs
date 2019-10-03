---
title: 语音 SDK 日志记录-语音服务
titleSuffix: Azure Cognitive Services
description: 在语音 SDK 中启用日志记录。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 31ff21e33860f75d91d01e80e3ee77bd7192f780
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559487"
---
# <a name="enable-logging-in-the-speech-sdk"></a>在语音 SDK 中启用日志记录

日志记录到文件是语音 SDK 的一项可选功能。 在开发日志记录期间, 会从语音 SDK 的核心组件提供其他信息和诊断。 可以通过将语音配置对象上的`Speech_LogFilename`属性设置为日志文件的位置和名称来启用此功能。 从该配置创建识别器后, 日志记录将被全局激活, 以后不能再禁用日志记录。 在运行日志记录会话期间, 不能更改日志文件的名称。

> [!NOTE]
> 由于在所有支持的语音 SDK 编程语言中都提供了语音 SDK 版本 1.4.0 (JavaScript 除外), 因此可以使用日志记录。

## <a name="sample"></a>样本

在配置对象上指定日志文件名。 作为示例, 假设你创建了一个名`config`为的实例: `SpeechConfig`

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

可以从 config 对象创建识别器。 这将为所有识别器启用日志记录。

> [!NOTE]
> 如果是从配置`SpeechSynthesizer`对象创建的, 则不会启用日志记录。 不过, 如果启用了日志记录, 则还会接收来自`SpeechSynthesizer`的诊断。

## <a name="create-a-log-file-on-different-platforms"></a>在不同的平台上创建日志文件

对于 Windows 或 Linux, 日志文件可以位于用户对其具有写入权限的任何路径中。 默认情况下, 对其他操作系统中的文件系统位置的写入权限可能受到限制或限制。

### <a name="universal-windows-platform-uwp"></a>通用 Windows 平台 (UWP)

UWP 应用程序需要将日志文件放在一个应用程序数据位置 (本地、漫游或临时) 中。 可以在本地应用程序文件夹中创建一个日志文件:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

[此处](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)提供了有关 UWP 应用程序的文件访问权限的详细信息。

### <a name="android"></a>Android

可以将日志文件保存到内部存储、外部存储或缓存目录。 在内部存储或缓存目录中创建的文件专用于应用程序。 最好是在外部存储中创建一个日志文件。

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

上述代码会将日志文件保存到特定于应用程序的目录的根目录中的外部存储。 用户可以使用文件管理器访问文件 (通常在中`Android/data/ApplicationName/logfile.txt`)。 卸载应用程序时, 将删除该文件。

还需要在清单文件`WRITE_EXTERNAL_STORAGE`中请求权限:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

有关适用于 Android 应用程序的数据和文件存储的详细信息, 请参阅[此处](https://developer.android.com/guide/topics/data/data-storage.html)。

#### <a name="ios"></a>iOS

只有应用程序沙盒中的目录是可访问的。 可以在文档、库和临时目录中创建文件。 文档目录中的文件可供用户使用。 下面的代码段演示如何在应用程序文档目录中创建日志文件:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

若要访问已创建的文件, 请将以下属性`Info.plist`添加到该应用程序的属性列表中:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

有关 iOS 文件系统的详细信息, 请参阅[此处](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的示例](https://aka.ms/csspeech/samples)
