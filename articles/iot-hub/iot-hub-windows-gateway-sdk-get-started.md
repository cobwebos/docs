---
title: "IoT 中心网关 SDK 入门 | Microsoft Docs"
description: "使用 Windows 的 Azure IoT 网关 SDK 演练，说明了使用 Azure IoT 网关 SDK 时应理解的关键概念。"
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
ms.date: 11/16/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: 2d8b98fbd5345edd5dc6891df12f05eccd8e7ca8
ms.openlocfilehash: 6f2fe4fd3442d97955519348416b35fe6f9075d1


---
# <a name="azure-iot-gateway-sdk---get-started-using-windows"></a>Azure IoT 网关 SDK - 开始使用 Windows
[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何生成示例
开始之前，必须[设置开发环境][lnk-setupdevbox]，以便在 Windows 上使用 SDK。

1. 打开 **VS2015 开发人员命令提示**命令提示符。
2. 浏览到本地 **azure-iot-gateway-sdk** 存储库副本中的根文件夹。
3. 运行 **tools\\build.cmd** 脚本。 此脚本创建 Visual Studio 解决方案文件，生成解决方案，并运行测试。 你可以在本地 **azure-iot-gateway-sdk** 存储库副本的 **build** 文件夹中找到 Visual Studio 解决方案。

## <a name="how-to-run-the-sample"></a>如何运行示例
1. **build.cmd** 脚本在本地存储库副本中创建一个名为 **build** 的文件夹。 此文件夹中包含本示例中使用的两个模块。
   
    生成脚本将 **logger.dll** 放在 **build\\modules\\logger\\Debug** 文件夹中，将 **hello_world.dll** 放在 **build\\modules\\hello_world\\Debug** 文件夹中。 按以下 JSON 设置文件中所示，将这些路径用于 **module path** 值。
2. Hello_world_sample 过程使用 JSON 配置文件的路径作为命令行中的参数。 以下示例 JSON 文件已在 **azure-iot-gateway-sdk\samples\hello_world\src\hello_world_win.json** 中作为存储库的一部分提供。 除非你修改了生成脚本，将模块或示例可执行文件放置在非默认位置，否则，此脚本将按原样运行。 

   > [!NOTE]
   > 模块路径相对于 hello_world_sample.exe 所在的目录。 示例 JSON 配置文件默认为在当前工作目录中写入“log.txt”。
   
    ```
    {
      "modules": [
        {
          "name": "logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
            }
          },
          "args": { "filename": "log.txt" }
        },
        {
          "name": "hello_world",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
            }
          },
          "args": null
          }
      ],
      "links": [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```
3. 浏览到 **azure-iot-gateway-sdk** 存储库的本地副本的根文件夹。

4. 运行以下命令：
   
   ```
   build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
   ```

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md



<!--HONumber=Nov16_HO5-->


