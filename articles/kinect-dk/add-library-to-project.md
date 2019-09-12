---
title: 将 Azure Kinect 库添加到 Visual Studio 项目
description: 将 Azure Kinect 库添加到项目
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 传感器, sdk, visual studio 2017, visual studio 2019, nuget
ms.openlocfilehash: 0adcb0baac9898a75886bcc1b835fb8e7b64b3ab
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801365"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>将 Azure Kinect 库添加到 Visual Studio 项目

本文引导你完成将 Azure Kinect NuGet 包添加到 Visual Studio 项目的过程。

## <a name="install-azure-kinect-nuget-package"></a>安装 Azure Kinect NuGet 包

若要安装 Azure Kinect NuGet 包：

1. 可在以下文章中找到有关在 Visual Studio 中安装 NuGet 包的详细说明：[快速入门：在 Visual Studio 中安装和使用包](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio)。
2. 若要添加该包，可以使用程序包管理器 UI：右键单击“引用”，然后从解决方案资源管理器中选择“管理 NuGet 包”。
3. 选择[nuget.org](https://www.nuget.org) 作为包源，选择“浏览”选项卡，然后搜索 `Microsoft.Azure.Kinect.Sensor`。
4. 从列表中选择该包并安装。

## <a name="use-azure-kinect-nuget-package"></a>使用 Azure Kinect NuGet 包

添加该包后，将标头文件中的 include 语句添加到源代码，例如：

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[现已准备好生成第一个应用程序](build-first-app.md)
