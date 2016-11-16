---
title: "IoT 中心网关 SDK 入门 | Microsoft Docs"
description: "此 Azure IoT 网关 SDK 演练使用 Linux 以说明在使用 Azure IoT 网关 SDK 时应理解的关键概念。"
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
ms.date: 08/25/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 23176a9251a90a985a5d2fbce23ceeb9d0925234


---
# <a name="azure-iot-gateway-sdk-beta-get-started-using-linux"></a>Azure IoT 网关 SDK（Beta 版）- 使用 Linux 入门
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何生成示例
开始之前，必须[设置开发环境][lnk-setupdevbox]，以便在 Linux 上使用 SDK。

1. 打开 shell。
2. 浏览到本地 **azure-iot-gateway-sdk** 存储库副本中的根文件夹。
3. 运行 **tools/build.sh** 脚本。 此脚本使用 **cmake** 实用工具在 **azure-iot-gateway-sdk** 存储库本地副本的根文件夹中创建一个名为 **build** 的文件夹，并生成一个生成文件。 然后，该脚本将生成解决方案并运行测试。

> [!NOTE]
> 每次运行 **build.sh** 脚本时，都会删除 **azure-iot-gateway-sdk** 存储库本地副本的根文件夹中的 **build** 文件夹并重新生成。
> 
> 

## <a name="how-to-run-the-sample"></a>如何运行示例
1. **build.sh** 脚本在 **azure-iot-gateway-sdk** 存储库的本地副本内的 **build** 文件夹中生成输出。 该文件夹中包含本示例中使用的两个模块。
   
    生成脚本将 **liblogger_hl.so** 放在 **build/modules/logger/** 文件夹中，将 **libhello_world_hl.so** 放在 **build/modules/hello_world/** 文件夹中。 按如下 JSON 设置文件所示，将这些路径用于 **module path** 值。
2. **samples/hello_world/src** 文件夹中的文件 **hello_world_lin.json** 是可用于运行示例的面向 Linux 的示例 JSON 设置文件。 如下所示的示例 JSON 设置假定你将从本地 **azure-iot-gateway-sdk** 存储库副本的根文件夹运行示例。
3. 对于 **logger_hl** 模块，请在 **args** 部分，将 **filename** 值设置为包含日志数据的文件的名称和路径。
   
    这是面向 Linux 的 JSON 设置文件的示例，该文件将写入从其运行示例的文件夹中的 **log.txt** 。
   
    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "loading args": {
            "module path" : "./build/modules/logger/liblogger_hl.so"
          },
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "loading args": {
            "module path" : "./build/modules/hello_world/libhello_world_hl.so"
          },
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```
4. 在 shell 中，浏览到 **azure-iot-gateway-sdk** 文件夹。
5. 运行以下命令：
   
   ```
   ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
   ``` 

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO2-->


