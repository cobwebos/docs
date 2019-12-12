---
title: 将 Azure IoT Edge 设备添加到 Azure IoT Central | Microsoft Docs
description: 以操作员身份将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e5d60c77e9bdc0733c12bca891eb6c3e33a1fceb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979064"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>教程：将 Azure IoT Edge 设备添加到 Azure IoT Central 应用程序

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教程介绍如何向 Azure IoT Central 应用程序添加和配置 Azure IoT Edge 设备。 在本教程中，我们从 Azure 市场中选择了支持 IoT Edge 的 Linux VM。

本教程由两个部分组成：

* 首先，作为操作员，你将了解如何对 IoT Edge 设备进行云优先预配。
* 然后，你将了解如何对 IoT Edge 设备进行“设备优先”预配。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加新的 IoT Edge 设备
> * 使用共享访问签名 (SAS) 密钥配置 IoT Edge 设备，以帮助进行预配
> * 在 IoT Central 中查看仪表板和模块运行状况
> * 将命令发送到在 IoT Edge 设备上运行的模块
> * 设置在 IoT Edge 设备上运行的模块的属性

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要一个 Azure IoT Central 应用程序。 按照[此快速入门创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)。

## <a name="enable-azure-iot-edge-enrollment-group"></a>启用 Azure IoT Edge 注册组
在“管理”页为 Azure IoT Edge 注册组启用 SAS 密钥  。

![“管理”页的屏幕截图，其中突出显示了“设备连接”](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>预配“云优先”Azure IoT Edge 设备  
在本部分中，你将使用环境传感器模板创建新的 IoT Edge 设备，并对该设备进行预配。 选择“设备” > “环境传感器模板”   。 

![“设备”页的屏幕截图，其中突出显示了“环境传感器模板”](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

选择“+ 新建”，然后输入所选设备 ID 和名称  。 选择“创建”  。

![“创建新设备”对话框的屏幕截图，其中突出显示了“设备 ID”和“创建”](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

设备变为“已注册”模式  。

![“环境传感器模板”页的屏幕截图，其中突出显示了“设备状态”](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>部署支持 IoT Edge 的 Linux VM

> [!NOTE]
> 可选择使用任何计算机或设备。 操作系统可以是 Linux 或 Windows。

在本教程中，我们使用在 Azure 上创建的支持 Azure IoT 的 Linux VM。 在 [Azure 市场](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)中，选择“立即获取”  。 

![Azure 市场的屏幕截图，其中突出显示了“立即获取”](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

选择“继续”。 

![“在 Azure 中创建此应用”对话框的屏幕截图，其中突出显示了“继续”](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


你将转到 Azure 门户。 选择“创建”  。

![Azure 门户的屏幕截图，其中突出显示了“创建”](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

选择“订阅”，创建新的资源组，并选择“(美国)美国西部 2”以实现 VM 可用性   。 然后，输入用户和密码信息。 在以后的步骤中需要用到这些信息，因此请记住它们。 选择“查看 + 创建”  。

![“创建虚拟机”详细信息页的屏幕截图，其中突出显示了各种选项](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

验证后，选择“创建”  。

![“创建虚拟机”页的屏幕截图，其中突出显示了“通过验证”和“创建”](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

创建资源需要几分钟时间。 选择“转到资源”。 

![部署完成页的屏幕截图，其中突出显示了“转到资源”](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>将 VM 预配为 IoT Edge 设备 

在“支持和疑难解答”下，选择“串行控制台”   。

![“支持和疑难解答”选项的屏幕截图，其中突出显示了“串行控制台”](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

你将看到如下所示的屏幕：

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

按 Enter，根据提示提供用户名和密码，然后再次按 Enter。 

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

若要以管理员身份（用户“root”）运行命令，请输入：**sudo su –**

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

检查 IoT Edge 运行时版本。 撰写本文时，当前的正式版为 1.0.8。

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

安装 vim 编辑器，或根据需要使用 nano。 

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

编辑 IoT Edge config.yaml 文件。

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

向下滚动并注释掉 yaml 文件的连接字符串部分。 

**之前**

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**之后**（按 Esc 并按小写字母 a，开始进行编辑。）

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

取消注释 yaml 文件的对称密钥部分。 

**之前**

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**之后**

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

转到 IoT Central。 获取 IoT Edge 设备的作用域 ID、设备 ID 和对称密钥。
![IoT Central 屏幕截图，其中突出显示了各种设备连接选项](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

转到 Linux 计算机，将作用域 ID 和注册 ID 替换为设备 ID 和对称密钥。

按 Esc 并键入“:wq!”  。 按 Enter 以保存更改。

重启 IoT Edge 以处理更改，然后按 Enter。

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

键入“iotedge list”  。 几分钟后，你会看到部署了三个模块。

![控制台屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central 设备资源管理器 

在 IoT Central 中，设备进入预配状态。

![IoT Central“设备”选项的屏幕截图，其中突出显示了“设备状态”](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

“模块”选项卡显示 IoT Central 上的设备和模块的状态  。 

![IoT Central“模块”选项卡的屏幕截图](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


从在前面步骤中创建的设备模板中，你将看到显示在窗体中的云属性。 输入值，然后选择“保存”  。 

![“我的 Linux Edge 设备”窗体的屏幕截图](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

下面是以仪表板磁贴形式显示的视图。

![“我的 Linux Edge 设备”仪表板磁贴的屏幕截图](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>后续步骤

现在你已了解如何在 IoT Central 中使用和管理 IoT Edge 设备，建议接下来执行以下步骤：

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [配置透明网关](../../iot-edge/how-to-create-transparent-gateway.md)
