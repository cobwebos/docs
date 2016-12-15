---
title: "Azure IoT 中心网关 SDK 入门 (Linux) | Microsoft 文档"
description: "了解如何在 Linux 计算机上构建网关，以及 Azure IoT 网关 SDK 的重要概念，如模块和 JSON 配置文件。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: cf537bdd-2352-4bb1-96cd-a283fcd3d6cf
ms.service: iot-hub
ms.devlang: cpp
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 6b41567f7b43fd6a56da1f571e007d31cef97b92
ms.openlocfilehash: 68965a1157b31d75595e546b2b227844ddff2eb9


---
# <a name="get-started-with-the-azure-iot-gateway-sdk-linux"></a>Azure IoT 中心网关 SDK 入门 (Linux)
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何生成示例
开始之前，必须[设置开发环境][lnk-setupdevbox]，以便在 Linux 上使用 SDK。

1. 打开 shell。
2. 浏览到本地 **azure-iot-gateway-sdk** 存储库副本中的根文件夹。
3. 运行 **tools/build.sh --skip-unittests** 脚本。 此脚本使用 **cmake** 实用工具在 **azure-iot-gateway-sdk** 存储库本地副本的根文件夹中创建一个名为 **build** 的文件夹，并生成一个生成文件。 然后，该脚本将生成解决方案并跳过单元测试。 如果想要生成并运行单元测试，请删除 **--skip-unittests** 参数。

> [!NOTE]
> 每次运行 **build.sh** 脚本时，都会删除 **azure-iot-gateway-sdk** 存储库本地副本的根文件夹中的 **build** 文件夹并重新生成。
> 
> 

## <a name="how-to-run-the-sample"></a>如何运行示例
1. **build.sh** 脚本在 **azure-iot-gateway-sdk** 存储库的本地副本内的 **build** 文件夹中生成输出。 该文件夹中包含本示例中使用的两个模块。
   
    生成脚本将 **liblogger.so** 放在 **build/modules/logger/** 文件夹中，将 **libhello_world.so** 放在 **build/modules/hello_world/** 文件夹中。 按如下 JSON 设置文件所示，将这些路径用于 **module path** 值。
2. Hello_world_sample 过程使用 JSON 配置文件的路径作为命令行中的参数。 **azure-iot-gateway-sdk/samples/hello_world/src/hello_world_win.json** 上的存储库中已提供示例 JSON 文件，下面也复制了该文件的内容。 除非你修改了生成脚本，将模块或示例可执行文件放置在非默认位置，否则，可按原样运行此脚本。

   > [!NOTE]
   > 模块路径相对于从中启动 hello_world_sample 可执行文件的当前工作目录，而不是可执行文件所在的目录。 示例 JSON 配置文件默认为在当前工作目录中写入“log.txt”。
   
    ```
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
3. 导航到 **azure-iot-gateway-sdk/build** 文件夹。
4. 运行以下命令：
   
   ```
   ./samples/hello_world/hello_world_sample ./../samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Dec16_HO1-->


