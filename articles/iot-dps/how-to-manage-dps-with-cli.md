---
title: "如何使用 Azure CLI 2.0 和 IoT 扩展管理设备预配服务 | Microsoft Docs"
description: "了解如何使用 Azure CLI 2.0 和 IoT 扩展来管理设备预配服务"
services: iot-dps
keywords: 
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 674245f1e284e7308474fed0f6c53b350ec1c819
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-cli-20-and-the-iot-extension-to-manage-device-provisioning-services"></a>如何使用 Azure CLI 2.0 和 IoT 扩展来管理设备预配服务

[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) 是一个开源跨平台命令行工具，用于管理 IoT Edge 等 Azure 资源。 Azure CLI 2.0 适用于 Windows、Linux 和 MacOS。 使用 Azure CLI 2.0 可以管理 Azure IoT 中心资源、设备预配服务实例和现成的链接中心。

IoT 扩展丰富了 Azure CLI 2.0 的功能，例如设备管理和完整的 IoT Edge 功能。

在本教程中，我们先完成设置 Azure CLI 2.0 和 IoT 扩展的步骤。 然后了解如何运行 CLI 命令来执行基本的设备预配服务操作。 

## <a name="installation"></a>安装 

### <a name="step-1---install-python"></a>步骤 1 - 安装 Python

需要 [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)。

### <a name="step-2---install-azure-cli-20"></a>步骤 2 - 安装 Azure CLI 2.0

遵照[安装说明](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)在环境中安装 Azure CLI 2.0。 Azure CLI 2.0 版本必须至少是 2.0.24 或更高。 请使用 `az –version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 在 Windows 上，一种简单的安装方法是下载并安装 [MSI](https://aka.ms/InstallAzureCliWindows)。

### <a name="step-3---install-iot-extension"></a>步骤 3 - 安装 IoT 扩展

[IoT 扩展自述文件](https://github.com/Azure/azure-iot-cli-extension)介绍了该扩展的多种安装方法。 最简单的方法是运行 `az extension add --name azure-cli-iot-ext`。 安装完成后，可以使用 `az extension list` 验证当前安装的扩展，或使用 `az extension show --name azure-cli-iot-ext` 查看有关 IoT 扩展的详细信息。 若要删除该扩展，可以使用 `az extension remove --name azure-cli-iot-ext`。


## <a name="basic-device-provisioning-service-operations"></a>基本的设备预配服务操作
该示例演示如何使用 CLI 命令登录到 Azure 帐户、创建 Azure 资源组（保存 Azure 解决方案相关资源的容器）、创建 IoT 中心、创建设备预配服务、列出现有的设备预配服务，并创建链接的 IoT 中心。 

在开始之前，请完成前面所述的安装步骤。 如果没有 Azure 帐户，可以立即[创建一个免费帐户](https://azure.microsoft.com/free/?v=17.39a)。 


### <a name="1-log-in-to-the-azure-account"></a>1.登录到 Azure 帐户
  
    az login

![登录][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2.在 eastus 中创建资源组 IoTHubBlogDemo

    az group create -l eastus -n IoTHubBlogDemo

![创建资源组][2]


### <a name="3-create-two-device-provisioning-services"></a>3.创建两个设备预配服务

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![创建 DPS][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4.列出此资源组下的所有现有设备预配服务

    az iot dps list --resource-group IoTHubBlogDemo

![列出 DPS][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5.在新建的资源组下创建 IoT 中心 blogDemoHub

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![创建 IoT 中心][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6.将一个现有的 IoT 中心链接到设备预配服务

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![链接中心][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 注册设备
> * 启动设备
> * 验证设备已注册

前往下一教程，了解如何跨负载均衡的中心预配多台设备。 

> [!div class="nextstepaction"]
> [跨负载均衡的 IoT 中心预配设备](./tutorial-provision-multiple-hubs.md)
