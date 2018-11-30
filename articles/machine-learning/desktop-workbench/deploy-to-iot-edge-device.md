---
title: 将 Azure 机器学习模型部署到 Azure IoT Edge 设备 | Microsoft Docs
description: 本文档介绍将 Azure 机器学习模型部署到 Azure IoT Edge 设备的方式。
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 792ac3f26bdea6c6ccb084d893925d60e6333edb
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852446"
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>将 Azure 机器学习模型部署到 Azure IoT Edge 设备

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Azure 机器学习模型可以容器化为基于 Docker 的 Web 服务。 Azure IoT Edge 允许你将容器远程部署到设备。 组合使用这些服务在边缘运行你的模型可以实现更快的响应时间和更少的数据传输。 

可以在 [AI Toolkit for Azure IoT Edge](https://aka.ms/AI-toolkit) 中找到其他脚本和说明。

## <a name="operationalize-the-model"></a>实施模型

Azure IoT Edge 模块基于容器映像。 若要将机器学习模型部署到 IoT Edge 设备，需要创建一个 Docker 映像。

按照 [Azure 机器学习模型管理 Web 服务部署](model-management-service-deploy.md)中的说明实施模型以通过模型创建 Docker 图像。

## <a name="deploy-to-azure-iot-edge"></a>部署到 Azure IoT Edge

在获得模型的映像后，可以将其部署到任何 Azure IoT Edge 设备。 所有机器学习模型都可在 IoT Edge 设备上运行。 

### <a name="set-up-an-iot-edge-device"></a>设置 IoT Edge 设备

使用 Azure IoT Edge 文档来准备设备。 

1. [将设备注册到 Azure IoT 中心](../../iot-edge/how-to-register-device-portal.md)。 此过程的输出是可用来配置物理设备的一个连接字符串。 
2. 在物理设备上安装 Azure IoT Edge 运行时，并为其配置连接字符串。 可以在 [Windows](../../iot-edge/how-to-install-iot-edge-windows-with-windows.md) 或 [Linux](../../iot-edge/how-to-install-iot-edge-linux.md) 设备上安装此运行时。  


### <a name="find-the-machine-learning-container-image-location"></a>查找机器学习容器映像位置
需要机器学习容器映像的位置。 若要查找容器映像的位置：

1. 登录到 [Azure 门户](http://portal.azure.com/)。
2. 在“Azure 容器注册表”中，选择要检查的注册表。
3. 在注册表中，单击“存储库”以查看所有存储库及其映像的列表。

在 Azure 门户中查看容器注册表时，检索容器注册表凭据。 需要将这些凭据提供给 IoT Edge 设备，以便它可以从你的专用注册表中拉取映像。 

1. 在容器注册表中，单击“访问密钥”。 
2. 如果尚未**启用**管理员用户，则将其启用。 
3. 保存“登录服务器”、“用户名”和“密码”的值。 

### <a name="deploy-the-container-image-to-your-device"></a>将容器映像部署到设备

有了容器映像和容器注册表凭据，你便可以将机器学习模型部署到 IoT Edge 设备。 

按照[从 Azure 门户部署 IoT Edge 模块](../../iot-edge/how-to-deploy-modules-portal.md)中的说明在 IoT Edge 设备上启动你的模型。 











