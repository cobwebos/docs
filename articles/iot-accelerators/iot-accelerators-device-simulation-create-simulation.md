---
title: 创建新的 IoT 模拟 - Azure | Microsoft Docs
description: 在本教程中，请使用设备模拟来创建并运行新的模拟。
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: 09a6920e0d3a50da1bdacbf2bc7a80396c885897
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "61448510"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>教程：创建并运行 IoT 设备模拟

在本教程中，请使用一个或多个模拟设备通过设备模拟来创建并运行新的 IoT 模拟。

本教程介绍以下操作：

>[!div class="checklist"]
> * 查看所有活动的和历史的模拟
> * 创建并运行新模拟
> * 查看模拟的指标
> * 停止模拟

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

若要按本教程操作，需在 Azure 订阅中部署设备模拟的实例。

如果尚未部署设备模拟，则应完成[在 Azure 中部署并运行 IoT 设备模拟](quickstart-device-simulation-deploy.md)快速入门。

## <a name="open-device-simulation"></a>打开设备模拟

若要在浏览器中运行设备模拟，请先导航到 [Microsoft Azure IoT 解决方案加速器](https://www.azureiotsolutions.com)。 

可能会要求使用 Azure 订阅凭据登录。

然后，单击在[快速入门](quickstart-device-simulation-deploy.md)中部署的设备模拟的磁贴上的“启动”。

## <a name="view-simulations"></a>查看模拟

在菜单栏中选择“模拟”。  “模拟”页显示所有可用模拟的相关信息。  在此页中，可以查看当前正运行的以及之前运行的模拟。 若要重新运行以前的模拟，请单击模拟磁贴以打开模拟详细信息：

![模拟](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>创建模拟

若要创建模拟，请单击右上角的“+ 新建模拟”。 

一个模拟包含一个或多个设备模型。 设备模型定义要模拟的设备的行为、遥测和消息格式。

若要添加设备模型，请单击“+ 添加设备类型”，然后从列表中选择设备模型。  模拟可以有多个设备模型。 每个设备模型可以有不同的设备计数和消息频率。

当新的模拟窗体完成后，请单击“开始模拟”  开始模拟。

配置和启动模拟可能需要数分钟的时间，具体取决于模拟设备的数目。

![新建模拟](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>停止模拟

单击“开始模拟”  后，会看到模拟详细信息页。 此详细信息页显示 IoT 中心提供的实时模拟统计信息和指标。 也可通过单击“模拟”页上模拟的磁贴导航到此详细信息页。 

若要停止模拟，请单击右上角操作栏中的“停止模拟”。 

![停止模拟](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建、运行和停止模拟， 以及如何查看模拟详细信息。 若要详细了解如何运行模拟，请继续阅读下一教程：

> [!div class="nextstepaction"]
> [创建自定义模拟设备](iot-accelerators-device-simulation-create-custom-device.md)
