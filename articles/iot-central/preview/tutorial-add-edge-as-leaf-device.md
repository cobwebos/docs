---
title: 将 Azure IoT Edge 设备添加到 Azure IoT Central | Microsoft Docs
description: 以操作员身份将 Azure IoT Edge 设备添加到 Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892633"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>教程：将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教程介绍如何向 Microsoft Azure IoT Central 应用程序添加和配置 Azure IoI Edge 设备  。 在本教程中，我们从 Azure 市场中选择了支持 Azure IoT Edge 的 Linux VM。

本教程由两个部分组成：

* 首先，作为操作员，你将了解如何对 Azure IoT Edge 设备进行云优先预配。
* 然后，你将了解如何对 Azure IoT Edge 设备进行设备优先预配。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加新的 Azure IoT Edge 设备
> * 使用 SAS 密钥配置 Azure IoT Edge 设备以帮助预配
> * 在 IoT Central 中查看仪表板和模块运行状况
> * 将命令发送到在 Azure IoT Edge 设备上运行的模块
> * 设置在 Azure IoT Edge 设备上运行的模块的属性

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要一个 Azure IoT Central 应用程序。 按照此快速入门[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)。

## <a name="enable-azure-iot-edge-enrollment-group"></a>启用 Azure IoT Edge 注册组
在“管理”页为 Azure IoT Edge 注册组启用 SAS 密钥。

![设备模板 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>云优先 Azure IoT Edge 设备预配   
在本部分中，你将使用环境传感器模板创建新的 Azure IoT Edge 设备，并预配设备  。 单击左侧导航栏上的“设备”，然后单击“环境传感器模板”。 

![设备模板 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

单击“+ 新建”，然后输入适合自己的设备 ID 和名称  。 

![设备模板 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

设备进入“已注册”模式  。

![设备模板 - Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>部署支持 Azure IoT Edge 的 Linux VM

>注意：可以选择使用任何计算机或设备。 OS：Linux 或 Windows）

在本教程中，我们选择了可在 Azure 上创建且支持 Azure IoT 的 Linux VM。 你将转到 [Azure 市场](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)，然后单击“立即获取”按钮  。 

![Azure 市场](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

单击“继续” 

![Azure 市场](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


你将转到 Azure 门户。 单击“创建”按钮 

![Azure 市场](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

选择“订阅”，最好创建新的资源组，对于 VM 可用性选择“美国西部 2”，输入用户和密码。 请记住，后续步骤将需要使用用户和密码。 单击“查看 + 创建” 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

验证后，单击“创建” 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

创建资源需要几分钟时间。 单击“转到资源” 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>将 VM 预配为 Azure IoT Edge 设备 

在左侧导航栏中的“支持 + 故障排除”下，单击“串行控制台”

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

将显示如下屏幕

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

按 Enter，并在系统出现提示时提供用户名和密码，然后按 Enter。 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

要以管理员/根用户身份运行命令，请运行以下命令：sudo su – 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

检查 Azure IoT Edge 运行时版本。 当前的正式版为 1.0.8

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

安装 vim 编辑器，或者根据偏好使用 nano。 

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

编辑 Azure IoT Edge config.yaml 文件

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

向下滚动并注释掉 yaml 文件的连接字符串部分。 

**之前**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**之前**（按 Esc 并按小写字母 a，以开始进行编辑）

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

取消评论 yaml 文件的对称密钥部分。 

**之前**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**之后**

![Ubuntu VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

转到 IoT Central，并获取 Azure IoT Edge 设备![设备连接](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)的作用域 ID、设备 ID 和对称密钥

转到 Linux 框中，将作用域 ID、注册 ID 替换为设备 ID 和对称密钥

按 Esc 并键入“:wq!”   然后按 Enter 以保存更改 

重启 Azure IoT Edge 以处理更改，然后按 Enter 

![设备连接](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

键入“iotedge list”，这将花费几分钟时间，你将看到部署的三个模块 

![设备连接](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central 设备资源管理器 

在 IoT Central 中，设备将进入预配状态

![设备连接](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

“模块”选项卡将显示 IoT Central 上的设备和模块的状态 

![设备连接](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


云属性将显示在窗体中（在上述步骤中创建的设备模板中）。 输入值，然后单击“保存”  。 

![设备连接](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

仪表板磁贴

![设备连接](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

本教程介绍了如何：

* 添加新的 Azure IoT Edge 设备
* 使用 SAS 密钥配置 Azure IoT Edge 设备以帮助预配
* 在 IoT Central 中查看仪表板和模块运行状况
* 将命令发送到在 Azure IoT Edge 设备上运行的模块
* 设置在 Azure IoT Edge 设备上运行的模块的属性

## <a name="next-steps"></a>后续步骤

现在你已了解如何在 IoT Central 中管理 Azure IoT Edge 设备，建议接下来执行以下步骤：

<!-- Next how-tos in the sequence -->

要了解如何配置透明网关，请遵循本教程

> [!div class="nextstepaction"]
> [配置透明网关](../../iot-edge/how-to-create-transparent-gateway.md)
