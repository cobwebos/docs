---
title: 更改和重新部署微服务 | Microsoft Docs
description: 本教程介绍如何在远程监视中更改和重新部署微服务
services: ''
suite: iot-suite
author: giyeh
manager: hegate
ms.author: giyeh
ms.service: iot-suite
ms.date: 04/19/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: b9be74b4ef5a1239f6ce753ebf41af6b5dbacb5e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
---
# <a name="customize-and-redeploy-a-microservice"></a>自定义和重新部署微服务

本教程介绍如何编辑远程监视解决方案中的微服务之一、生成微服务的映像、将映像部署到 Docker 中心，然后在远程监视解决方案中使用该映像。 为了介绍此概念，本教程使用了一个简单场景：在其中调用微服务 API，并将状态消息从“Alive and Well”更改为“New Edits Made Here!”

远程监视解决方案使用基于 Docker 映像生成的微服务，这些映像是从 Docker 中心提取的。 

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 在远程监视解决方案中编辑并生成微服务
> * 生成 Docker 映像
> * 将 Docker 映像推送到 Docker 中心
> * 提取新 Docker 映像
> * 可视化更改 

## <a name="prerequisites"></a>先决条件

若要学习本教程，需要：

>[!div class="checklist"]
> * [在本地部署远程监视预配置解决方案](../iot-accelerators/iot-accelerators-remote-monitoring-deploy-local.md)
> * [Docker 帐户](https://hub.docker.com/)
> * [Postman](https://www.getpostman.com/) - 用于查看 API 响应

## <a name="call-the-api-and-view-response-status"></a>调用 API 并查看响应状态

在此部分，我们调用默认的 IoT 中心管理器微服务 API。 该 API 返回一条状态消息，稍后我们将通过自定义微服务对其进行更改。

1. 确保远程监视解决方案在计算机本地运行。
2. 找到下载的 Postman 并打开它。
3. 在 Postman 中的 GET 内输入以下内容：http://localhost:8080/iothubmanager/v1/status。
4. 查看返回的结果，应会看到 "Status": "OK:Alive and Well"。
![Alive and Well Postman 消息](media/iot-suite-microservices-example/postman-alive-well.png)

## <a name="change-the-status-and-build-the-image"></a>更改状态并生成映像

现在，将 Iot 中心管理器微服务的状态消息更改为“New Edits Made Here!”， 然后使用此新状态重新生成 Docker 映像。 如果在此处遇到问题，请参阅[故障排除](#Troubleshoot)部分。

1. 确保终端处于打开状态，切换到远程监视解决方案所克隆到的目录。 
2. 将目录切换到“..azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/WebService/v1/Controllers”。
3. 在任何文本编辑器或偏好的 IDE 中打开 StatusController.cs。 
4. 找到以下代码：

    ```javascript
    return new StatusApiModel(true, "Alive and well");
    ```

    将其更改为以下代码并保存。

    ```javascript
    return new StatusApiModel(true, "New Edits Made Here!");
    ```

5. 返回终端，但这次请切换到以下目录：“...azure-iot-pcs-remote-monitoring-dotnet/iothub-manager/scripts/docker”。
6. 若要生成新的 Docker 映像，请键入

    ```cmd/sh
    sh build
    ```

7. 若要验证是否已成功创建新映像，请键入

    ```cmd/sh
    docker images 
    ```

存储库应为“azureiotpcs/iothub-manager-dotnet”。

![成功创建 Docker 映像](media/iot-suite-microservices-example/successful-docker-image.png)

## <a name="tag-and-push-the-image"></a>标记并推送映像
在将新的 Docker 映像推送到 Docker 中心之前，Docker 要求对映像进行标记。 如果在此处遇到问题，请参阅[故障排除](#Troubleshoot)部分。

1. 键入以下代码，找到创建的 Docker 映像的映像 ID：

    ```cmd/sh
    docker images
    ```

2. 若要使用“testing”标记映像，请键入

    ```cmd/sh
    docker tag [Image ID] [docker ID]/iothub-manager-dotnet:testing 
    ```

3. 若要将新标记的映像推送到 Docker 中心，请键入

    ```cmd/sh
    docker push [docker ID]/iothub-manager-dotnet:testing
    ```

4. 打开 Internet 浏览器，转到 [Docker 中心](https://hub.docker.com/)并登录。
5. 现在，Docker 中心应会显示新推送的 Docker 映像。
![Docker 中心内的 Docker 映像](media/iot-suite-microservices-example/docker-image-in-docker-hub.png)

## <a name="update-your-remote-monitoring-solution"></a>更新远程监视解决方案
现在需要更新本地 docker-compose.yml，以从 Docker 中心提取新 Docker 映像。 如果在此处遇到问题，请参阅[故障排除](#Troubleshoot)部分。

1. 返回终端，并切换到以下目录：“..azure-iot-pcs-remote-monitoring-dotnet/scripts/local”。
2. 在任何文本编辑器或偏好的 IDE 中打开 docker-compose.yml。
3. 找到以下代码：

    ```docker
    image: azureiotpcs/pcs-auth-dotnet:testing
    ```

    将其更改为下图所示的内容，并保存。

    ```cmd/sh
    image: [docker ID]/pcs-auth-dotnet:testing
    ```

## <a name="view-the-new-response-status"></a>查看新响应状态
最后，重新部署远程监视解决方案的本地实例，并在 Postman 中查看新状态响应。

1. 返回终端，并切换到以下目录：“..azure-iot-pcs-remote-monitoring-dotnet/scripts/local”。
2. 在终端中键入以下命令，启动远程监视解决方案的本地实例：

    ```cmd/sh
    docker-compose up
    ```

3. 找到下载的 Postman 并打开它。
4. 在 Postman 中的 GET 内输入以下请求：http://localhost:8080/iothubmanager/v1/status。 现在应会看到 "Status": "OK: New Edits Made Here!"。

![New Edits Made Here Postman 消息](media/iot-suite-microservices-example/new-postman-message.png)

## <a name="Troubleshoot"></a>故障排除

如果遇到问题，请尝试删除本地计算机上的 Docker 映像和容器。

1. 若要删除所有容器，首先需要停止所有正在运行的容器。 打开终端并键入

    ```cmd/sh
    docker stop $(docker ps -aq)
    docker rm $(docker ps -aq)
    ```
    
2. 若要删除所有映像，请打开终端并键入 

    ```cmd/sh
    docker rmi $(docker images -q)
    ```

3. 可以键入以下代码，检查计算机上是否有任何容器

    ```cmd/sh
    docker ps -aq 
    ```

    如果已成功删除所有容器，则不会显示任何结果。

4. 可以键入以下代码，检查计算机上是否有任何映像

    ```cmd/sh
    docker images
    ```

    如果已成功删除所有容器，则不会显示任何结果。

## <a name="next-steps"></a>后续步骤

本教程介绍如何执行下列操作：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 在远程监视解决方案中编辑并生成微服务
> * 生成 Docker 映像
> * 将 Docker 映像推送到 Docker 中心
> * 提取新 Docker 映像
> * 可视化更改 

请继续尝试[在远程监视解决方案中自定义设备模拟器微服务](iot-suite-remote-monitoring-test.md)

有关可供开发人员参考的远程监视解决方案详细信息，请参阅：

* [开发人员参考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
<!-- Next tutorials in the sequence -->

