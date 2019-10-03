---
title: 快速入门 - 将 Azure Kinect 传感器流录制到文件中
description: 有关如何将传感器 SDK 发出的数据流录制到文件的快速入门
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: azure, kinect, 录制, 播放, 读取器, matroska, mkv, 流, 深度, rgb, 相机, 彩色, imu, 音频, 传感器
ms.openlocfilehash: 18e01f81d80ca1a1c4cf9b1f599748aff943697a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451328"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>快速入门：将 Azure Kinect 传感器流录制到文件中

本快速入门提供有关如何使用 [Azure Kinect 录制器](azure-kinect-recorder.md)工具将传感器 SDK 发出的数据流录制到文件的信息。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本快速入门假设：

- 已将 Azure Kinect DK 连接到主机电脑，并已正常通电。
- 已完成硬件[设置](set-up-azure-kinect-dk.md)。

## <a name="create-recording"></a>创建录制内容

1. 打开命令提示符，提供 [Azure Kinect 录制器](azure-kinect-recorder.md)的路径，即 `k4arecorder.exe` 工具安装目录的位置。 例如：`C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`。
2. 录制 5 秒。

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. 默认情况下，该录制器使用 NFOV 非装箱深度模式，输出 1080p RGB @ 30 fps 的内容（包括 IMU 数据）。 有关录制选项的完整概述和提示，请参阅 [Azure Kinect 录制器](azure-kinect-recorder.md)。

## <a name="play-back-recording"></a>播放录制内容

可以使用 [Azure Kinect 查看器](azure-kinect-viewer.md)播放录制内容。

1. 启动 [`k4aviewer.exe`](azure-kinect-viewer.md)
2. 展开“打开录制内容”选项卡并打开你的录制内容。 

## <a name="next-steps"></a>后续步骤

你现已了解如何将传感器流录制到文件中，接下来可以

> [!div class="nextstepaction"]
> [生成第一个 Azure Kinect 应用程序](build-first-app.md)
