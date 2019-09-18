---
title: Azure IoT Central 中的设备连接 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 中的设备连接相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bb7711eea927212042ed2299bae74130867c1692
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067645"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central 中的设备连接

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

本文介绍与 Microsoft Azure IoT Central 中的设备连接相关的重要概念。

Azure IoT Central 使用[Azure IoT 中心设备预配服务（DPS）](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)来管理所有设备注册和连接。

使用 DPS 启用：

- IoT Central 支持大规模载入和连接设备。
- 您可以生成设备凭据并使设备脱机配置，而无需通过 IoT Central UI 注册设备。
- 使用共享访问签名（SAS）连接的设备。
- 使用行业标准的 x.509 证书进行连接的设备。
- 你使用自己的设备 Id 在 IoT Central 中注册设备。 使用自己的设备 Id 可简化与现有后端办公系统的集成。
- 将设备连接到 IoT Central 的一种一致的方式。

本文介绍了以下四种用例：

- [使用 SAS 快速连接单个设备](#connect-a-single-device)
- [使用 SAS 以大规模连接设备](#connect-devices-at-scale-using-sas)
- [使用 x.509 证书大规模连接设备](#connect-devices-using-x509-certificates)这是建议用于生产环境的方法。
- [直接连接而无需首先注册设备](#connect-without-registering-devices)

## <a name="connect-a-single-device"></a>连接单个设备

当你尝试 IoT Central 或测试设备时，此方法非常有用。 你可以使用 IoT Central 应用程序中的设备连接信息，通过设备预配服务（DPS）将设备连接到 IoT Central 应用程序。 可以在以下语言中找到示例 DPS 设备客户端代码：

- [C\#](./howto-connect-raspberry-pi-csharp.md)
- [Node.js](./howto-connect-nodejs.md)

## <a name="connect-devices-at-scale-using-sas"></a>使用 SAS 以大规模连接设备

若要使用 SAS 将设备连接到 IoT Central 大规模，需注册并设置设备：

### <a name="register-devices-in-bulk"></a>批量注册设备

若要将大量设备注册到 IoT Central 的应用程序，请使用 CSV 文件[导入设备 id 和设备名称](howto-manage-devices.md#import-devices)。

若要检索导入的设备的连接信息，请[从 IoT Central 应用程序导出 CSV 文件](howto-manage-devices.md#export-devices)。

> [!NOTE]
> 若要了解如何在不先将设备注册 IoT Central 的情况下连接设备，请参阅 "[连接而不先注册设备](#connect-without-registering-devices)"。

### <a name="set-up-your-devices"></a>设置设备

使用设备代码中的导出文件中的连接信息，使设备能够连接到 IoT Central 应用程序并将数据发送到 IoT。 有关连接设备的详细信息，请参阅[后续步骤](#next-steps)。

## <a name="connect-devices-using-x509-certificates"></a>使用 x.509 证书连接设备

在生产环境中，建议使用 x.509 证书作为 IoT Central 的设备身份验证机制。 有关详细信息，请参阅[使用 X.509 CA 证书进行设备身份验证](../iot-hub/iot-hub-x509ca-overview.md)。

以下步骤说明如何使用 x.509 证书将设备连接到 IoT Central：

1. 在 IoT Central 应用程序中，添加并验证用于生成设备证书_的中间或根 x.509 证书_：

    - 导航到 "**管理" > 设备连接 "> 证书（x.509）** "，并添加要用于生成叶设备证书的 x.509 根证书或中间证书。

      ![连接设置](media/concepts-connectivity/connection-settings.png)

      如果有安全漏洞，或者主证书设置为过期，请使用辅助证书来减少停机时间。 更新主证书时，可以继续使用辅助证书设置设备。

    - 验证证书所有权可确保证书的上载程序具有证书的私钥。 验证证书：
        - 选择 "**验证代码**" 旁边的按钮以生成代码。
        - 使用在上一步中生成的验证码创建 x.509 验证证书。 将证书保存为 .cer 文件。
        - 上载已签名的验证证书，然后选择 "**验证**"。

          ![连接设置](media/concepts-connectivity/verify-cert.png)

1. 使用 CSV 文件在 IoT Central 应用程序中_导入和注册设备_。

1. _设置设备。_ 使用上传的根证书生成叶证书。 使用**设备 ID**作为叶证书中的 CNAME 值。 设备 ID 应该全部小写。 然后用预配服务信息对设备进行编程。 当设备在第一台设备上打开时，它会从 DPS 检索 IoT Central 应用程序的连接信息。

### <a name="further-reference"></a>进一步参考

- [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases) 的示例实现。

- [C 编写的示例设备客户端](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)

### <a name="for-testing-purposes-only"></a>仅用于测试目的

仅适用于测试，你可以使用这些实用程序来生成 CA 证书和设备证书。

- 如果使用 DevKit 设备，此[命令行工具](https://aka.ms/iotcentral-docs-dicetool)将生成一个 CA 证书，你可以将该证书添加到 IoT Central 应用程序以验证证书。

- 使用此[命令行工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )可以：
  - 创建证书链。 遵循 GitHub 一文中的步骤2。
  - 将证书保存为 .cer 文件，以上载到 IoT Central 应用程序。
  - 使用 IoT Central 应用程序中的验证代码生成验证证书。 遵循 GitHub 一文中的步骤3。
  - 使用设备 Id 作为工具的参数为设备创建叶证书。 遵循 GitHub 一文中的步骤4。

## <a name="connect-without-registering-devices"></a>不注册设备连接

IoT Central 的主要方案是，Oem 可以批量制造可连接到 IoT Central 应用程序的设备，而无需先进行注册。 制造商必须生成合适的凭据，并在工厂中配置设备。 当设备首次打开时，它会自动连接到 IoT Central 的应用程序。 IoT Central 操作员必须先批准设备，然后才能发送数据。

下图概述了此流程：

![连接设置](media/concepts-connectivity/device-connection-flow1.png)

以下步骤更详细地介绍了此过程。 步骤会稍有不同，具体取决于你使用的是 SAS 证书还是 x.509 证书进行设备身份验证：

1. 配置连接设置：

    - **X.509 证书：** [添加并验证根/中间证书](#connect-devices-using-x509-certificates)，并在以下步骤中使用它来生成设备证书。
    - **SAS：** 复制主密钥。 此密钥是 IoT Central 应用程序的组 SAS 密钥。 在以下步骤中使用密钥生成设备 SAS 密钥。
    ![连接设置 SAS](media/concepts-connectivity/connection-settings-sas.png)

1. 生成设备凭据
    - **证书 x.509：** 使用添加到 IoT Central 应用程序的根或中间证书，为设备生成叶证书。 请确保使用小写的**设备 ID**作为叶证书中的 CNAME。 仅出于测试目的，使用此[命令行工具](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )生成设备证书。
    - **SAS：** 使用此[命令行工具](https://www.npmjs.com/package/dps-keygen)生成设备 SAS 密钥。 使用上一步中的 "组**主密钥**"。 设备 ID 必须为小写。

      若要安装[密钥生成器实用程序](https://github.com/Azure/dps-keygen)，请运行以下命令：

      ```cmd/sh
      npm i -g dps-keygen
      ```

      若要从组 SAS 主密钥生成设备密钥，请运行以下命令：

      ```cmd/sh
      dps-keygen -mk:<Primary_Key(GroupSAS)> -di:<device_id>
      ```

1. 若要设置设备，请通过**作用域 id**、**设备 ID**和**x.509 设备证书**或**SAS 密钥**来闪存每个设备。

1. 然后打开设备，使其连接到 IoT Central 应用程序。 当你在设备上切换时，它将首先连接到 DPS 以检索其 IoT Central 的注册信息。

1. 连接的设备最初显示为 " **Device Explorer** " 页上的 "未**关联的设备**"。 设备预配状态为“已注册”。 **将**设备与相应的设备模板关联，并批准设备以连接到 IoT Central 应用程序。 然后，设备可以从 IoT 中心检索连接字符串并开始发送数据。 现在已完成设备预配，预配状态为 "已**预**配"。

## <a name="provisioning-status"></a>当前设置状态

当真实设备连接到 IoT Central 应用程序时，其预配状态将如下所示：

1. 首先**注册**设备预配状态。 此状态表示设备是在 IoT Central 中创建的，并且具有设备 ID。 设备在以下情况时注册：
    - 新的实际设备将添加到 " **Device Explorer** " 页上。
    - 使用 "**导入**" 在**Device Explorer**页上添加一组设备。
    - 设备未在**Device Explorer**页面上手动注册，但已使用有效的凭据进行连接，并且在**Device Explorer**页上显示为未**关联**的设备。

1. 当连接到具有有效凭据的 IoT Central 应用程序的设备完成设置步骤时，设备预配状态将更改为 "已**设置**"。 在此步骤中，设备将从 IoT 中心检索连接字符串。 设备现在可以连接到 IoT 中心并开始发送数据。

1. 操作员会阻止设备。 当某个设备被阻止时，它无法将数据发送到您的 IoT Central 应用程序。 被阻止的设备的设置状态为 "已**阻止**"。 操作员必须先重置设备，然后才能继续发送数据。 当操作员取消阻止设备时，预配状态将恢复为其以前的值，即**已注册或已** **设置**。

## <a name="sdk-support"></a>SDK 支持

Azure 设备 Sdk 为实现设备代码提供了最简单的方法。 以下设备 SDK 已发布：

- [适用于 C 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [适用于 Python 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [适用于 Node.js 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [适用于 Java 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [适用于 .NET 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 功能和 IoT 中心连接

与 IoT 中心进行的所有设备通信都使用以下 IoT 中心连接选项：

- [设备到云的消息传递](../iot-hub/iot-hub-devguide-messages-d2c.md)
- [设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)

下表对 Azure IoT Central 设备功能映射到 IoT 中心功能的具体情况进行了汇总：

| Azure IoT Central | Azure IoT 中心 |
| ----------- | ------- |
| 度量：遥测 | 设备到云的消息传递 |
| 设备属性 | 设备孪生报告属性 |
| 设置 | 设备孪生所需的和报告的属性 |

若要详细了解如何使用设备 SDK，请查看下面的某篇文章以获取示例代码：

- [将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序](howto-connect-nodejs.md)
- [将 Raspberry Pi 设备连接到 Azure IoT Central 应用程序](howto-connect-raspberry-pi-python.md)
- [将 DevDiv 工具包设备连接到 Azure IoT Central 应用程序](howto-connect-devkit.md)。

### <a name="protocols"></a>协议

设备 SDK 支持下述用于连接到 IoT 中心的网络协议：

- MQTT
- AMQP
- HTTPS

若要了解这些不同的协议以及选择协议的指南，请参阅[选择通信协议](../iot-hub/iot-hub-devguide-protocols.md)。

如果设备无法使用这些受支持协议中的任何一种，可以使用 Azure IoT Edge 进行协议转换。 IoT Edge 支持其他边缘智能方案，可以将处理从 Azure IoT Central 应用程序卸载到边缘。

## <a name="security"></a>安全性

在设备与 Azure IoT Central 之间交换的所有数据都经过加密。 如果设备已连接到任何面向设备的 IoT 中心终结点，则 IoT 中心会对从该设备发出的所有请求进行身份验证。 为了避免通过网络交换凭据，设备使用签名的令牌进行身份验证。 有关详细信息，请参阅[控制对 IoT 中心的访问](../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>后续步骤

现在，你已了解 Azure IoT Central 中的设备连接，下面是建议的后续步骤：

- [准备和连接 DevKit 设备](howto-connect-devkit.md)
- [准备和连接 Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序](howto-connect-nodejs.md)
- [C SDK：预配设备客户端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
