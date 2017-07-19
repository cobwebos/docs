---
title: "Azure IoT Edge 入门 (Linux) | Microsoft Docs"
description: "了解如何在 Linux 计算机上构建网关，以及 Azure IoT Edge 的重要概念，如模块和 JSON 配置文件。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/07/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: b02d79fcd9cd2a2ef0041aac4e85528263c8d58a
ms.contentlocale: zh-cn
ms.lasthandoff: 06/09/2017


---
# <a name="explore-azure-iot-edge-architecture-on-linux"></a>在 Linux 上浏览 Azure IoT Edge 体系结构

[!INCLUDE [iot-hub-iot-edge-getstarted-selector](../../includes/iot-hub-iot-edge-getstarted-selector.md)]

[!INCLUDE [iot-hub-iot-edge-install-build-linux](../../includes/iot-hub-iot-edge-install-build-linux.md)]

## <a name="how-to-run-the-sample"></a>如何运行示例

**build.sh** 脚本在 **iot-edge** 存储库的本地副本内的 **build** 文件夹中生成输出。 该输出包括此示例中使用的两个 IoT Edge 模块。

生成脚本将 **liblogger.so** 放在 **build/modules/logger/** 文件夹中，将 **libhello\_world.so** 放在 **build/modules/hello_world/** 文件夹中。 按以下示例 JSON 设置文件中所示，将这些路径用于 module path 值。

hello\_world\_sample 过程使用 JSON 配置文件的路径作为命令行参数。 以下示例 JSON 文件在 SDK 存储库的以下路径中提供：**samples/hello\_world/src/hello\_world\_lin.json**。 除非修改了生成脚本，将 IoT Edge 模块或示例可执行文件放置在非默认位置，否则，此配置文件可按原样工作。

> [!NOTE]
> 模块路径相对于从中启动 hello\_world\_sample 可执行文件的当前工作目录，而不是可执行文件所在的目录。 示例 JSON 配置文件默认为在当前工作目录中写入“log.txt”。

```json
{
    "modules" :
    [
        {
            "name" : "logger",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/logger/liblogger.so"
            }
            },
            "args" : {"filename":"log.txt"}
        },
        {
            "name" : "hello_world",
            "loader": {
            "name": "native",
            "entrypoint": {
                "module.path": "./modules/hello_world/libhello_world.so"
            }
            },
            "args" : null
        }
    ],
    "links":
    [
        {
            "source": "hello_world",
            "sink": "logger"
        }
    ]
}
```

1. 浏览到 iot-edge 存储库本地副本根路径中的“build”文件夹。

1. 运行以下命令：

    ```sh
    ./samples/hello_world/hello_world_sample ../samples/hello_world/src/hello_world_lin.json`
    ```

[!INCLUDE [iot-hub-iot-edge-getstarted-code](../../includes/iot-hub-iot-edge-getstarted-code.md)]


