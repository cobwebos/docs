---
title: include 文件
description: include 文件
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "33814622"
---
当 Functions 主机在本地运行时，它会将日志写入以下路径：

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

在 Windows 上，`<DefaultTempDirectory>` 是 TMP、TEMP、USERPROFILE 环境变量或 Windows 目录的第一个找到的值。
在 MacOS 或 Linux 上，`<DefaultTempDirectory>` 是 TMPDIR 环境变量。

> [!NOTE]
> 当 Functions 主机启动时，它会覆盖目录中的现有文件结构。