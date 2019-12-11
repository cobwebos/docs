---
title: 如何使用 Azure IoT 中心设备预配服务的自动预配功能将 MXChip IoT DevKit 注册到 IoT 中心 | Microsoft Docs
description: 如何使用 Azure IoT 中心设备预配服务（DPS）自动预配功能将 MXChip IoT DevKit 注册到 IoT 中心。
author: liydu
ms.author: liydu
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: f05e92f0452b1cfff23e2094354203fd7eaea48b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975646"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>使用 Azure IoT 中心设备预配服务的自动预配功能将 MXChip IoT DevKit 注册到 IoT 中心

本文介绍如何使用 Azure IoT 中心设备预配服务的[自动预配](concepts-auto-provisioning.md)功能将 MXChip IoT DevKit 注册到 Azure IoT 中心 本教程介绍如何执行以下操作：

* 在设备上配置设备预配服务的全局终结点。
* 使用唯一设备机密 (UDS) 生成 X.509 证书。
* 登记单个设备。
* 验证设备是否已注册。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是具有多种外设和传感器的集成 Arduino 兼容板。 可以使用 Visual Studio Code 中的 [Azure IoT Device Workbench](https://aka.ms/iot-workbench) 或 [Azure IoT 工具](https://aka.ms/azure-iot-tools)扩展包进行开发。 DevKit 附带一个不断增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，指导你构建物联网 (IoT) 解决方案的原型，以利用 Azure 服务。

## <a name="before-you-begin"></a>开始之前

若要完成本教程中的步骤，请先执行以下任务：

* 按照[将 IoT DEVKIT AZ3166 连接到云中的 Azure IoT 中心](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started#prepare-the-development-environment)中的 "准备开发环境" 一节中的步骤，配置 DevKit 的 wi-fi 并准备好开发环境。
* 参考[更新 DevKit 固件](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)教程升级到最新固件（1.3.0 或以上）。
* 按照[使用 Azure 门户设置 IoT 中心设备预配服务](/azure/iot-dps/quick-setup-auto-provision)中的步骤，创建 IoT 中心并将其链接到设备预配服务实例。

## <a name="open-sample-project"></a>打开示例项目

1. 确保 IoT DevKit **未连接**到计算机。 先启动 VS Code，然后将 DevKit 连接到计算机。

1. 单击 "`F1`" 打开命令面板，键入并选择 " **Azure IoT 设备工作台：打开示例 ...** "。然后选择 " **IoT DevKit** as 板"。

1. 在 IoT Workbench“示例”页中，找到“使用 DPS 进行设备注册”，然后单击“打开示例”。 然后选择用于下载示例代码的默认路径。
    ![打开示例](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>将唯一设备机密保存在设备安全存储上

可以根据设备的[证明机制](concepts-security.md#attestation-mechanism)在设备上配置自动预配。 MXChip IoT DevKit 使用[受信任计算组](https://trustedcomputinggroup.org)提供的[设备标识组合引擎](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf)。 保存在 DevKit 上 STSAFE 安全芯片 ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) 中的唯一设备机密 (UDS) 用于生成设备的唯一 [X.509 证书](concepts-security.md#x509-certificates)。 稍后在设备预配服务中和运行时的注册过程中，可以使用该证书完成注册过程。

典型的 UDS 是长度为 64 个字符的字符串，如以下示例所示：

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

在 DevKit 上保存 UDS：

1. 在 VS Code 中，单击状态栏以选择 DevKit 的 COM 端口。
  ![选择 COM 端口](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. 在 DevKit 上，按下**按钮 a**，按下 "**重置**" 按钮，然后松开**按钮 A**。DevKit 进入配置模式。

1. 单击 "`F1`" 打开命令面板，键入并选择 " **Azure IoT 设备工作台：配置设备设置 ..."> 配置唯一设备字符串（UDS）** 。
  ![配置 UDS](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. 请记下生成的 UDS 字符串。 将需要该字符串来生成 X.509 证书。 然后按 `Enter`。
  ![复制 UDS](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. 从通知中确认已在 STSAFE 上成功配置 UDS。
  ![配置 UDS 成功](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> 或者，可以使用 Putty 等实用程序通过串行端口配置 UDS。 请按照[使用配置模式](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/)中的说明执行此操作。

## <a name="update-the-global-device-endpoint-and-id-scope"></a>更新全局设备终结点和 ID 范围

在设备代码中，需要指定[设备预配终结点](/azure/iot-dps/concepts-service#device-provisioning-endpoint)和 ID 范围以确保租户隔离。

1. 在 Azure 门户中，选择设备预配服务的“概述”窗格，记下“全局设备终结点”和“ID 范围”的值。
  ![设备预配服务全局终结点和 ID 范围](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. 打开 **DevKitDPS.ino**。 找到 `[Global Device Endpoint]` 和 `[ID Scope]` 并将其替换为刚刚记下的值。
  ![设备预配服务终结点](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. 在代码中填充 `registrationId` 变量。 仅允许使用最多 128 个字符的字母数字、小写和连字符组合。 同样记下该值。
  ![注册 ID](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. 单击 "`F1`"，键入并选择 " **Azure IoT 设备工作台：上传设备代码**"。 它随后开始编译代码并上传到 DevKit。
  ![设备上传](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>生成 X.509 证书

此示例使用的[证明机制](/azure/iot-dps/concepts-device#attestation-mechanism)是 X.509 证书。 需要使用实用程序来生成它。

1. 在 VS Code 中，单击 `F1`，键入并选择“打开新终端”以打开终端窗口。

1. 在 `tool` 文件夹中运行 `dps_cert_gen.exe`。

1. 将编译的二进制文件位置指定为 `..\.build\DevKitDPS`。 然后粘贴刚刚记下的 UDS 和 registrationId。 
  ![生成 X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. `.pem` X.509 证书在同一文件夹中生成。
  ![X.509 文件](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>创建设备注册项

1. 在 Azure 门户中，打开“设备预配服务”，导航到“管理注册”部分，然后单击“添加单个注册”。
  ![添加单个注册](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. 单击“主要证书 .pem 或 .cer 文件”旁边的文件图标，上传生成的文件 `.pem`。
  ![上传 .pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>验证 DevKit 是否已注册到 Azure IoT 中心

按 DevKit 上的“重置”按钮。 应该在 DevKit 屏幕上看到 “DPS 已连接！”。 设备重启后，会执行以下操作：

1. 设备会向设备预配服务发送注册请求。
1. 设备预配服务回复注册质询，设备需对此做出响应。
1. 注册成功后，设备预配服务会向设备发送 IoT 中心 URI、设备 ID 和加密密钥。
1. 设备上的 IoT 中心客户端应用程序连接到你的中心。
1. 成功连接到中心后，设备出现在 IoT 中心的 Device Explorer 中。
  ![设备已注册](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅 Iot DevKit [常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或通过以下支持渠道联系我们：

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何使用设备标识组合引擎将设备安全登记到设备预配服务，使设备能够自动注册到 Azure IoT 中心。 

概括而言，我们已了解如何：

> [!div class="checklist"]
> * 在设备上配置设备预配服务的全局终结点。
> * 使用唯一设备机密生成 X.509 证书。
> * 登记单个设备。
> * 验证设备是否已注册。

了解如何[创建和预配模拟设备](./quick-create-simulated-device.md)。

