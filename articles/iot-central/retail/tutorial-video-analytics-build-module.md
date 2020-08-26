---
title: 教程 - 修改 IoT Edge 实时视频分析模块
description: 本教程说明如何修改和生成视频分析 - 对象和运动检测应用程序模板所使用的实时视频分析网关模块。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d21eb8d8d79ec04f0f7e766b4eeb370811553e64
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037854"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>教程：修改和生成实时视频分析网关模块

本教程说明如何为实时视频分析 (LVA) 模块修改 IoT Edge 模块代码。

之前的教程使用模块的预生成映像。

## <a name="prerequisites"></a>先决条件

完成本教程中的步骤需要具备以下各项：

* [Node.js](https://nodejs.org/en/download/) v10 或更高版本
* 已安装 [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) 扩展的 [Visual Studio Code](https://code.visualstudio.com/Download)
* [Docker](https://www.docker.com/products/docker-desktop) 引擎
* 用于承载模块版本的 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* [Azure 媒体服务](https://docs.microsoft.com/azure/media-services/)帐户。 如果已完成之前的教程，可以再次使用之前创建的项。

## <a name="clone-the-repository"></a>克隆存储库

如果尚未克隆存储库，请使用以下命令将其克隆到本地计算机上的合适位置：

```cmd
git clone https://github.com/Azure/live-video-analytics
```

使用 VS Code 打开本地 live-video-analytics 存储库文件夹。

## <a name="edit-the-deploymentamd64json-file"></a>编辑 deployment.amd64.json 文件

1. 如果尚未这样操作，请在 lva-gateway 存储库的本地副本中创建一个名为 ref-apps/lva-edge-iot-central-gateway/storage 的文件夹。 Git 会忽略此文件夹，以防止意外签入任何机密信息。
1. 将文件 deployment.amd64.json 从 setup 文件夹复制到 storage 文件夹  。
1. 在 VS Code 中，打开 storage/deployment.amd64.json 文件。
1. 编辑 `registryCredentials` 部分以添加 Azure 容器注册表凭据。
1. 编辑 `LvaEdgeGatewayModule` 模块部分，以将映像名称和 AMS 帐户名称添加到 `env:amsAccountName:value`。
1. 编辑 `lvaYolov3` 模块部分并添加映像的名称。
1. 编辑 `lvaEdge` 模块部分并添加映像的名称。
1. 有关如何完成配置的详细信息，请参阅[在 Azure IoT Central 中创建视频分析应用程序](tutorial-video-analytics-create-app.md)。

## <a name="build-the-code"></a>生成代码

1. 首次尝试生成代码前，请使用 VS Code 终端运行 `npm install` 命令。 此命令安会装所需的包并运行设置脚本。

    > [!TIP]
    > 如果拉取较新版本的存储库 GitHub 存储库，请删除 node_modules 文件夹，然后再次运行 `npm install`。

1. 编辑 ./setup/imageConfig.json 文件以更新基于容器注册表名称命名的映像：

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. 使用 VS Code 终端运行 `docker login [your server].azurecr.io` 命令。 使用在部署清单中为模块提供的凭据。

1. 使用 VS Code 终端运行 npm version patch 命令。 该生成脚本会将映像部署到容器注册表。 VS Code 终端窗口中的输出表明生成是否成功。

1. 每一次完成生成时，LvaEdgeGatewayModule 映像的版本都会递增。 需要在部署清单文件中使用此版本。

## <a name="next-steps"></a>后续步骤

了解了视频分析 - 对象和运动检测应用程序模板以及 LVA IoT Edge 模块后，建议下一步详细了解以下信息：

> [!div class="nextstepaction"]
> [使用 Azure IoT Central 生成零售解决方案](overview-iot-central-retail.md)
