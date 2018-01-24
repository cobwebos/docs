---
title: "部署设备模拟解决方案 - Azure | Microsoft Docs"
description: "本教程展示了如何从 azureiotsuite.com 预配设备模拟解决方案。"
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/18/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: da9fb95ed5d3387c98c3274a53769d3f5f945371
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2017
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>部署 Azure IoT 设备模拟解决方案

本教程展示了如何预配设备模拟解决方案。 从 azureiotsuite.com 部署解决方案。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 配置设备模拟解决方案
> * 部署设备模拟解决方案
> * 登录到设备模拟解决方案

## <a name="prerequisites"></a>先决条件

需要有效的 Azure 订阅才能完成此教程。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="deploy-the-solution"></a>部署解决方案

在将解决方案部署到 Azure 订阅之前，必须选择一些配置选项：

1. 使用你的 Azure 帐户凭据登录到 [azureiotsuite.com](https://www.azureiotsuite.com)，并单击 **+** 来创建新的解决方案：

    ![创建新的解决方案](media/iot-suite-device-simulation-deploy/createnewsolution.png)

1. 单击“设备模拟”磁贴上的“选择”：

    ![选择“设备模拟”](media/iot-suite-device-simulation-deploy/select.png)

1. 在“创建设备模拟解决方案”页面上，为设备模拟解决方案输入**解决方案名称**。

1. 选择要用于预配解决方案的“订阅”和“区域”。

1. 指定是否希望随设备模拟解决方案部署新的 IoT 中心。 如果选中此框，则会将一个新的 IoT 中心部署到你的订阅。 无论是否选择，都可以在任何 IoT 中心指向你的模拟。

1. 单击“创建解决方案”  开始预配过程。 此过程通常需要数分钟的运行时间：

    ![设备模拟解决方案详细信息](media/iot-suite-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>登录到解决方案

在预配过程完成后，可以登录到设备模拟解决方案。

1. 在“预设的解决方案”页面上，单击新的设备模拟解决方案下的“启动”：

    ![选择新的解决方案](media/iot-suite-device-simulation-deploy/newsolution.png)

1. 可以在显示的面板中查看有关设备模拟解决方案的信息。 选择“解决方案仪表板”以连接到设备模拟解决方案。

    > [!NOTE]
    > 使用完设备模拟解决方案后，可以将其从此面板中删除。

    ![解决方案面板](media/iot-suite-device-simulation-deploy/properties.png)

1. 设备模拟解决方案仪表板将显示在浏览器中。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 配置解决方案
> * 部署解决方案
> * 登录到解决方案

现在，你已部署了设备模拟解决方案，下一步是[探究设备模拟解决方案的功能](./iot-suite-device-simulation-explore.md)。

<!-- Next tutorials in the sequence -->
