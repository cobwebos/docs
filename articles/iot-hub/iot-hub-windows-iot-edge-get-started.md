---
title: "Azure IoT Edge 入门 (Windows) | Microsoft Docs"
description: "了解如何在 Windows 计算机上构建 Azure IoT Edge 网关，以及 Azure IoT Edge 的重要概念，如模块和 JSON 配置文件。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 262963250921ceb6f1a2fed0f68c36a9f052b47b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>在 Windows 上浏览 Azure IoT Edge 体系结构

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-windows](../../includes/iot-hub-iot-edge-install-build-windows.md)]

## <a name="run-the-sample"></a>运行示例

build.cmd 脚本在 iot-edge 存储库的本地副本内的 "build" 文件夹中生成输出。 此输出包括许多文件，但本示例重点介绍三个文件：
- 两个 IoT Edge 模块：**build\\modules\\logger\\Debug\\logger.dll** 和 **build\\modules\\hello_world\\Debug\\hello\_world.dll**。 
- 可执行文件：**build\\samples\\hello\_world\\Debug\\hello\_world\_sample.exe**。 此过程使用 JSON 配置文件作为命令行参数。

在此示例中使用的第四个文件不在生成文件夹中，但克隆它时 iot-edge 存储库中包括：
- JSON 配置文件：**samples\\hello\_world\\src\\hello\_world\_win.json**。 此文件包含两个模块的路径。 它还声明 logger.dll 将其输出写入到的位置。 默认值是当前工作目录中的 **log.txt**。 

   >[!NOTE]
   >如果移动示例模块，或者为测试添加自己的模块，请更新配置文件中的 **module.path** 值以进行匹配。 模块路径相对于 **hello\_world\_sample.exe** 所在的目录。 

若要运行该示例，请遵循以下步骤：

1. 浏览到 iot-edge 存储库本地副本根路径中的“build”文件夹。

1. 运行以下命令：

   ```cmd
   samples\hello_world\Debug\hello_world_sample.exe ..\samples\hello_world\src\hello_world_win.json
   ```

1. 下面的输出表示示例成功运行：

   ```cmd
   gateway successfully created from JSON
   gateway shall run until ENTER is pressed
   ```
  
1. 按 **Enter** 键停止该进程。


[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]
