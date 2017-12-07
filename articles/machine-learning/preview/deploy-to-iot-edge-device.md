---
title: "将 Azure 机器学习模型部署到 Azure IoT Edge 设备 | Microsoft Docs"
description: "本文档介绍将 Azure 机器学习模型部署到 Azure IoT Edge 设备的方式。"
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/10/2017
ms.openlocfilehash: 60db74535edd09841a1b660c2bf832af65fbfcad
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>将 Azure 机器学习模型部署到 Azure IoT Edge 设备

所有容器化为基于 Docker 的 Web 服务的 Azure 机器学习模型还可以在 Azure IoT Edge 设备上运行。 可以在 [AI Toolkit for Azure IoT Edge](http://aka.ms/AI-toolkit) 中找到其他脚本和说明。

## <a name="operationalize-the-model"></a>实施模型
按照 [Azure 机器学习模型管理 Web 服务部署](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy)中的说明实施模型以通过模型创建 Docker 图像。

## <a name="deploy-to-azure-iot-edge"></a>部署到 Azure IoT Edge
Azure IoT Edge 将云分析和自定义业务逻辑移动到设备。 所有机器学习模型都可在 IoT Edge 设备上运行。 可以在 [aka.ms/azure-iot-edge-doc](https://aka.ms/azure-iot-edge-doc) 上找到设置 IoT Edge 设备和创建部署的文档。

以下是其他需要注意的事项。

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>将注册表凭据添加到 Edge 设备的 Edge 运行时
在运行 IoT Edge 的计算机上，添加注册表的凭据，以便运行时有权拉取容器。

在 Windows 上，运行以下命令：
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
在 Linux 上，运行以下命令：
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>查找机器学习容器映像位置
需要机器学习容器映像的位置。 若要查找容器映像的位置：

1. 登录到 [Azure 门户](http://portal.azure.com/)。
2. 在“Azure 容器注册表”中，选择要检查的注册表。
3. 在注册表中，单击“存储库”以查看所有存储库及其映像的列表。













