---
title: 在 Azure Stack Edge Pro GPU 设备上部署 GPU 模块 |Microsoft Docs
description: 介绍如何启用计算，并通过本地 UI 使 Azure Stack Edge Pro 设备计算就绪。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 1f16ef0ede25f17acb915a7812ae5b15b45f78a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899719"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上部署支持 GPU 的 IoT 模块

本文介绍如何在 Azure Stack Edge Pro GPU 设备上部署已启用 GPU IoT Edge 模块。 

在本文中，学习如何：
  - 准备 Azure Stack Edge Pro 以运行 GPU 模块。
  - 从 Git 存储库下载并安装示例代码。
  - 生成解决方案并生成部署清单。
  - 将解决方案部署到 Azure Stack Edge Pro 设备。
  - 监视模块输出。


## <a name="about-sample-module"></a>关于示例模块

本文中的 GPU 示例模块包括针对 GPU 的 CPU 的 PyTorch 和 TensorFlow 基准测试示例代码。

## <a name="prerequisites"></a>必备条件

在开始之前，请确保：

- 你可以访问已启用 GPU 的1节点 Azure Stack Edge Pro 设备。 此设备已使用 Azure 中的资源激活。 请参阅 [激活设备](azure-stack-edge-gpu-deploy-activate.md)。
- 已在此设备上配置计算。 按照 [教程：在 Azure Stack Edge Pro 设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)中的步骤操作。
- Azure 容器注册表 (ACR) 。 中转到 " **访问密钥** " 边栏选项卡，记下 "ACR 登录服务器"、"用户名" 和 "密码"。 有关详细信息，请参阅 [快速入门：使用 Azure 门户创建专用容器注册表](../container-registry/container-registry-get-started-portal.md#create-a-container-registry)。
- Windows 客户端上的以下开发资源：
    - [Azure CLI 2.0 或更高版本](https://aka.ms/installazurecliwindows)
    - [DOCKER CE](https://store.docker.com/editions/community/docker-ce-desktop-windows)。 你可能需要创建一个帐户来下载并安装该软件。
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。    
    - [适用于 Visual Studio Code 的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - 用于安装 Python 包的 Pip (通常包含在 Python 安装中) 

## <a name="get-the-sample-code"></a>获取示例代码

1. 请参阅 [azure 中的 Azure 智能边缘模式示例](https://github.com/azure-samples/azure-intelligent-edge-patterns)。 克隆或下载代码的 zip 文件。 

    ![下载 zip 文件](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    从 zip 文件中提取文件。 还可以克隆示例。

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>生成和部署模块

1. 在 Visual Studio Code 中打开 " **GpuReferenceModules** " 文件夹。

    ![在 VS Code 中打开 GPUReferenceModules](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. 打开 * 上的deployment.template.js* ，并标识它为容器注册表引用的参数。 在以下文件中，将使用 CONTAINER_REGISTRY_USERNAME、CONTAINER_REGISTRY_PASSWORD 和 CONTAINER_REGISTRY_NAME。

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. 创建新的文件。 填写容器注册表参数的值， (使用之前步骤中标识的参数) ，如下所示： 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    下面显示了一个示例 *env* 文件：
    
    ![创建并保存 env 文件](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. 在**SampleSolution**文件夹中将文件另存为*env。*

5. 若要登录 Docker，请在 Visual Studio Code 集成终端中输入以下命令。 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    在 Azure 门户中，访问容器注册表的 " **访问密钥** " 部分。 复制并使用注册表名称、密码和登录服务器。

    ![容器注册表中的访问密钥](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    提供凭据后，登录成功。

    ![成功登录](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. 将映像推送到 Azure 容器注册表。 在 "VS Code 资源管理器" 中，选择并右键单击 **deployment.template.js** 文件，然后选择 " **生成并推送 IoT Edge 解决方案**"。 

    ![生成并推送 IoT Edge 解决方案](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    如果未安装 Python 和 Python 扩展，则将在你构建并推送解决方案时安装这些扩展。 但这会导致生成时间较长。 

    完成此步骤后，你会在容器注册表中看到该模块。

    ![容器注册表中的模块](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. 若要创建部署清单，请右键单击 ** 上的deployment.template.js** ，然后选择 " **生成 IoT Edge 部署清单**"。 

    ![生成 IoT Edge 部署清单](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    通知会通知你生成部署清单的路径。 清单是 `deployment.amd64.json` 在 **config** 文件夹中生成的文件。 

8. 选择 "**配置**" 文件夹中的 "deployment.amd64.js"，然后选择 "**创建单个设备的部署** **"** 。 不要对文件使用 **deployment.template.js** 。 

    ![为单个设备创建部署](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    在 " **输出** " 窗口中，应会看到 "部署成功" 消息。

    ![已在输出中成功部署](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>监视模块  

1. 在 VS Code 命令面板中，运行“Azure IoT 中心: 选择 IoT 中心”。****

2. 选择包含要配置的 IoT Edge 设备的订阅和 IoT 中心。 在这种情况下，请选择用于部署 Azure Stack Edge Pro 设备的订阅，并选择为 Azure Stack Edge Pro 设备创建的 IoT Edge 设备。 在前面的步骤中通过 Azure 门户配置计算时，会发生这种情况。

3. 在 VS Code 资源管理器中，展开 "Azure IoT 中心" 部分。 在 " **设备**" 下，应会看到与 Azure Stack Edge Pro 设备对应的 IoT Edge 设备。 

    1. 选择该设备，右键单击并选择 " **开始监视" "内置事件终结点**"。
  
        ![开始监视](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. 中转到 " **设备" > 模块** ，你会看到你的 **GPU 模块** 正在运行。

        ![IoT 中心中的模块](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. VS Code 终端还应显示 IoT 中心事件作为 Azure Stack Edge Pro 设备的监视输出。

        ![监视输出](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        你可以看到，执行同一组操作所需的时间 (5000 的形状转换) 迭代的时间比 CPU 的小。

## <a name="next-steps"></a>后续步骤

- 详细了解如何 [将 GPU 配置为使用模块](azure-stack-edge-j-series-configure-gpu-modules.md)。
