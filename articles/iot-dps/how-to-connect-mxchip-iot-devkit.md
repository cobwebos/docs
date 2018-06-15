---
title: 如何使用 Azure IoT 中心设备预配服务的自动预配功能将 MXChip IoT DevKit 注册到 IoT 中心 | Microsoft Docs
description: 如何使用 Azure IoT 中心设备预配服务的自动预配功能将 MXChip IoT DevKit 注册到 IoT 中心。
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 331e589aadf783fc40ab6efbfb554469c27e48d8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629671"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>使用 Azure IoT 中心设备预配服务的自动预配功能将 MXChip IoT DevKit 注册到 IoT 中心

本文介绍如何使用 Azure IoT 中心设备预配服务的[自动预配](concepts-auto-provisioning.md)功能将 MXChip IoT DevKit 注册到 Azure IoT 中心 本教程介绍如何执行下列操作：

* 在设备上配置设备预配服务的全局终结点。
* 使用唯一设备机密 (UDS) 生成 X.509 证书。
* 登记单个设备。
* 验证设备是否已注册。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是具有多种外设和传感器的集成 Arduino 兼容板。 可以使用[适用于 Arduino 的 Visual Studio Code 扩展](https://aka.ms/arduino)针对其进行开发。 DevKit 附带一个不断增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，指导你构建物联网 (IoT) 解决方案的原型，以利用 Azure 服务。

## <a name="before-you-begin"></a>开始之前

若要完成本教程中的步骤，请先执行以下任务：

* 遵循[将 IoT DevKit AZ3166 连接到云中的 Azure IoT 中心](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)中的步骤准备好 DevKit。
* 参考[更新 DevKit 固件](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)教程升级到最新固件（1.3.0 或以上）。
* 遵循[使用 Azure 门户设置 IoT 中心设备预配服务](/azure/iot-dps/quick-setup-auto-provision)中的步骤，创建 IoT 中心并将其链接到设备预配服务实例。

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>生成自动预配注册软件并将其部署到设备

将 DevKit 连接到创建的设备预配服务实例：

1. 在 Azure 门户中，选择设备预配服务的“概述”窗格，记下“全局设备终结点”和“ID 范围”的值。
  ![DPS 全局终结点和 ID 范围](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. 确保在计算机上安装 `git` 并将其添加到可供命令窗口访问的环境变量。 参阅[软件自由保护组织的 Git 客户端工具](https://git-scm.com/download/)安装最新版本。

3. 打开命令提示符。 克隆设备预配服务示例代码的 GitHub 存储库：
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. 打开 Visual Studio Code，将 DevKit 连接到计算机，然后打开包含所克隆代码的文件夹。

5. 打开 **DevKitDPS.ino**。 找到 `[Global Device Endpoint]` 和 `[ID Scope]` 并将其替换为刚刚记下的值。
  ![DPS 终结点](./media/how-to-connect-mxchip-iot-devkit/endpoint.png)可将 **registrationId** 留空。 应用程序会根据 MAC 地址和固件版本自动生成一个 ID。 如果想要自定义注册 ID，只能使用字母数字、小写字母和连字符的组合，并且长度不能超过 128 个字符。 有关详细信息，请参阅[使用 Azure 门户管理设备登记](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments)。

6. 在 VS Code 中使用 Quick Open（Windows：`Ctrl+P`；macOS：`Cmd+P`），并键入 *task device-upload* 生成代码并将其上传到 DevKit。

7. 输出窗口中会显示任务是否成功。

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>将唯一设备机密保存在 STSAFE 安全芯片上

可以根据设备的[证明机制](concepts-security.md#attestation-mechanism)在设备上配置自动预配。 MXChip IoT DevKit 使用[受信任计算组](https://trustedcomputinggroup.org)提供的[设备标识组合引擎](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf)。 保存在 DevKit 上的 STSAFE 安全芯片中的*唯一设备机密 (UDS)* 用于生成唯一的 [X.509](concepts-security.md#x509-certificates) 设备证书。 稍后在设备预配服务中和运行时的注册过程中，可以使用该证书完成注册过程。

典型的唯一设备机密是长度为 64 个字符的字符串，如以下示例所示：

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

两个字符中的每个字符用作安全计算中的十六进制值。 上述示例 UDS 解析为：`0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`。

将唯一设备机密保存在 DevKit 上：

1. 使用 Putty 之类的工具打开串行监视器。 参阅[使用配置模式](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/)了解详细信息。

2. 将 DevKit 连接到计算机后，按住按钮 **A**，然后按下“重置”按钮并松开，进入配置模式。 屏幕将显示 DevKit ID 和“配置”。

3. 采用示例 UDS 字符串，并将一个或多个字符更改为自己 UDS 的 `0` 与 `f` 之间的其他值。

4. 在串行监视器窗口中，键入 *set_dps_uds [your_own_uds_value]* 并按 Enter。
  > [!NOTE]
  > 例如，如果通过将最后两个字符更改为 `f` 设置自己的 UDS，则需要输入类似于 `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff` 的命令。

5. 在未关闭串行监视器窗口的情况下，在 DevKit 上按“重置”按钮。

6. 记下“DevKit MAC 地址”和“DevKit 固件版本”值。
  ![固件版本](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>生成 X.509 证书

### <a name="windows"></a>Windows

1. 打开文件资源管理器，转到包含所克隆的设备预配服务示例代码的文件夹。 在 **.build** 文件夹中，找到 **DPS.ino.bin** 和 **DPS.ino.map** 并将其复制到包含代码的文件夹中。
  ![生成的文件](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > 如果已将 Arduino 的 `built.path` 配置更改为其他文件夹，则需要在配置的文件夹中找到这些文件。

2. 将这两个文件粘贴到与 **.build** 文件夹处于同一级别的 **tools** 文件夹中。

3. 运行 **dps_cert_gen.exe**。 遵照提示输入 DevKit 的“UDS”和“MAC 地址”以及“固件版本”，以生成 X.509 证书。
  ![运行 dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. 生成 X.509 证书后，会将一个 **.pem** 证书保存到同一文件夹中。

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>在设备预配服务中创建设备注册条目

1. 在 Azure 门户中转到设备预配服务实例。 选择 **管理登记** ，然后选择 **单独登记** 选项卡。![个人登记](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. 选择 **添加** 。

3. 在“添加注册”面板中，请执行以下操作：
   - 在“机制”下选择“X.509”
   - 在“主证书 .pem 或 .cer 文件”下单击“选择文件”
   - 在“打开的文件”对话框中，导航到刚生成的 **.pem** 证书并将其上传
   - 将其余的设置保留默认值，然后单击“保存”

   ![上传证书](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

## <a name="start-the-devkit"></a>启动 DevKit

1. 打开 VS Code 和串行监视器。

2. 按 DevKit 上的“重置”按钮。

将会看到 DevKit 开始注册到设备预配服务。

![VS Code 输出](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>验证 DevKit 是否已注册到 Azure IoT 中心

设备启动后，会执行以下操作：

1. 设备向设备预配服务发送注册请求。
2. 设备预配服务回复注册质询，设备需对此做出响应。
3. 注册成功后，设备预配服务会向设备发送 IoT 中心 URI、设备 ID 和加密密钥。
4. 设备上的 IoT 中心客户端应用程序连接到你的中心。
5. 成功连接到中心后，设备出现在 IoT 中心的 Device Explorer 中。
  ![设备已注册](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>更改设备 ID

在 Azure IoT 中心注册的默认设备 ID 为 *AZ3166*。 若要修改此 ID，请遵照[自定义设备 ID](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/) 中的说明。

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅 Iot DevKit [常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或通过以下支持渠道联系我们：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [堆栈溢出](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解如何使用设备标识组合引擎将设备安全登记到设备预配服务，使设备能够自动注册到 Azure IoT 中心。 

概括而言，我们已了解如何：

> [!div class="checklist"]
> * 在设备上配置设备预配服务的全局终结点。
> * 使用唯一设备机密生成 X.509 证书。
> * 登记单个设备。
> * 验证设备是否已注册。

了解如何[创建和预配模拟设备](./quick-create-simulated-device.md)。

