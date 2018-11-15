---
title: 部署自定义设备模拟映像 - Azure | Microsoft Docs
description: 本操作指南介绍如何将设备模拟解决方案的自定义 Docker 映像部署到 Azure。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285319"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>部署自定义设备模拟 Docker 映像

可以修改设备模拟解决方案以添加自定义功能。 例如，[使用协议缓冲区将遥测数据序列化](iot-accelerators-device-simulation-protobuf.md)一文介绍了如何将自定义设备添加到使用协议缓冲区 (Protobuf) 发送遥测数据的解决方案。 在本地测试更改后，下一步是将更改部署到 Azure 中的设备模拟实例。 若要完成此任务，需要创建并部署包含已修改的服务的 Docker 映像。

本操作指南中的步骤介绍如何：

1. 准备开发环境
1. 生成新的 Docker 映像
1. 将设备模拟配置为使用新的 Docker 映像
1. 使用新映像运行模拟

## <a name="prerequisites"></a>先决条件

若要完成本操作指南中的步骤，需要：

* 一个已部署的[设备模拟](quickstart-device-simulation-deploy.md)实例。
* Docker。 下载适用于你的平台的 [Docker Community Edition](https://www.docker.com/products/docker-engine#/download)。
* 一个可在其中上传 Docker 映像的 [Docker 中心帐户](https://hub.docker.com/)。 在 Docker 中心帐户内，创建名为 **device-simulation** 的公共存储库。
* 本地计算机上已修改并测试的[设备模拟解决方案](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)。 例如，可以修改解决方案，以[使用协议缓冲区将遥测数据序列化](iot-accelerators-device-simulation-protobuf.md)。
* 一个可以运行 SSH 的 shell。 如果安装了 Git For Windows，则可以使用附带安装的 **bash** shell。 也可以使用 [Azure Cloud Shell](https://shell.azure.com/)。

本文中的说明假定使用 Windows 操作系统。 如果使用另一操作系统，则可能需要调整某些文件路径和命令以适应环境。

## <a name="create-a-new-docker-image"></a>创建新的 Docker 映像

若要将自己的更改部署到设备模拟服务，需要编辑 **scripts\docker** 文件夹中的生成和部署脚本，以将容器上传到 Docker 中心帐户

### <a name="modify-the-docker-scripts"></a>修改 Docker 脚本

使用 Docker 中心存储库信息修改 **scripts\docker** 文件夹中的 Docker **build.cmd**、**publish.cmd** 和 **run.cmd** 脚本。 这些步骤假设已创建名为 **device-simulation** 的公共存储库：

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

按如下所示更新 **docker-compose.yml** 文件：

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>将解决方案配置为包含任何新文件

如果添加了任何新的设备模型文件，则需要显式将其包含在解决方案中。 对于所要包含的每个附加文件，将一个条目添加到 **services/services.csproj**。 例如，如果完成了[使用协议缓冲区将遥测数据序列化](iot-accelerators-device-simulation-protobuf.md)操作指南，请添加以下条目：

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>生成新的 Docker 映像并推送到 Docker 中心

使用 Docker 中心帐户将新的 Docker 映像发布到 Docker 中心：

1. 打开命令提示符，并导航到设备模拟存储库的本地副本。

1. 导航到 **docker** 文件夹：

    ```cmd
    cd scripts\docker
    ```

1. 运行以下命令以生成 Docker 映像：

    ```cmd
    build.cmd
    ```

1. 运行以下命令，将 Docker 映像发布到 Docker 中心存储库。 使用 Docker 中心凭据登录到 Docker：

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>更新服务

若要将设备模拟容器更新为使用自定义映像，请完成以下步骤：

* 使用 SSH 连接到托管设备模拟实例的虚拟机。 使用在上一部分记下的 IP 地址和密码：

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* 导航到 **/app** 目录：

    ```sh
    cd /app
    ```

* 编辑 **docker-compose.yml** 文件：

    ```sh
    sudo nano docker-compose.yml
    ```

    修改**映像**，将上传的自定义 **device-simulation** 映像指向 Docker 中心存储库：

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    保存所做更改。

* 运行以下命令重启微服务：

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>运行模拟

现在，可以使用自定义的设备模拟解决方案来运行模拟：

1. 从 [Microsoft Azure IoT 解决方案加速器](https://www.azureiotsolutions.com/Accelerators#dashboard)启动设备模拟 web UI。

1. 使用 Web UI 配置并运行模拟。 如果之前已完成[使用协议缓冲区将遥测数据序列化](iot-accelerators-device-simulation-protobuf.md)，则现在可以使用你的自定义设备模型。

## <a name="next-steps"></a>后续步骤

了解如何部署自定义设备模拟映像之后，建议接下来了解如何[将现有的 IoT 中心与设备模拟解决方案加速器配合使用](iot-accelerators-device-simulation-choose-hub.md)。