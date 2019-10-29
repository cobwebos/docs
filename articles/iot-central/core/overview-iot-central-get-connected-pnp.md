---
title: Azure IoT Central 中的设备连接 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 中的设备连接相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 5948efe35be9f8015a068134d9eec503e6ef5f48
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991997"
---
# <a name="get-connected-to-azure-iot-central-preview-features"></a>连接到 Azure IoT Central （预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文介绍与 Microsoft Azure IoT Central 中的设备连接相关的重要概念。

Azure IoT Central 使用[Azure IoT 中心设备预配服务（DPS）](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)来管理所有设备注册和连接。

使用 DPS 启用：

- IoT Central 支持大规模载入和连接设备。
- 您可以生成设备凭据并使设备脱机配置，而无需通过 IoT Central UI 注册设备。
- 使用共享访问签名（SAS）连接的设备。
- 使用行业标准的 x.509 证书进行连接的设备。
- 你使用自己的设备 Id 在 IoT Central 中注册设备。 使用自己的设备 Id 可简化与现有后端办公系统的集成。
- 将设备连接到 IoT Central 的一种一致的方式。

本文介绍以下用例：

- [使用 SAS 快速连接单个设备](#connect-a-single-device)
- [使用 SAS 以大规模连接设备](#connect-devices-at-scale-using-sas)
- [使用 x.509 证书大规模连接设备](#connect-devices-using-x509-certificates)这是建议用于生产环境的方法。
- [直接连接而无需首先注册设备](#connect-without-registering-devices)
- [使用 IoT 即插即用功能连接设备](#connect-devices-with-iot-plug-and-play)

## <a name="connect-a-single-device"></a>连接单个设备

当你尝试 IoT Central 或测试设备时，此方法非常有用。 你可以使用 IoT Central 应用程序中的设备连接信息，通过设备预配服务（DPS）将设备连接到 IoT Central 应用程序。 可以在以下语言中找到示例 DPS 设备客户端代码：

- [C\#](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device)
- [Node.js](https://github.com/Azure-Samples/azure-iot-samples-node/tree/master/provisioning/Samples/device)

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

在生产环境中，建议使用 x.509 证书作为 IoT Central 的设备身份验证机制。 有关详细信息，请参阅[使用 X.509 CA 证书进行设备身份验证](../../iot-hub/iot-hub-x509ca-overview.md)。

以下步骤说明如何使用 x.509 证书将设备连接到 IoT Central：

1. 在 IoT Central 应用程序中，添加并验证用于生成设备证书_的中间或根 x.509 证书_：

    - 导航到 "**管理" > 设备连接 "> 证书（x.509）** "，并添加要用于生成叶设备证书的 x.509 根证书或中间证书。

      ![连接设置](media/overview-iot-central-get-connected-pnp/connection-settings.png)

      如果有安全漏洞，或者主证书设置为过期，请使用辅助证书来减少停机时间。 更新主证书时，可以继续使用辅助证书设置设备。

    - 验证证书所有权可确保证书的上载程序具有证书的私钥。 验证证书：
        - 选择 "**验证代码**" 旁边的按钮以生成代码。
        - 使用在上一步中生成的验证码创建 x.509 验证证书。 将证书保存为 .cer 文件。
        - 上载已签名的验证证书，然后选择 "**验证**"。

          ![连接设置](media/overview-iot-central-get-connected-pnp/verify-cert.png)

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

![连接设置](media/overview-iot-central-get-connected-pnp/device-connection-flow1.png)

以下步骤更详细地介绍了此过程。 步骤会稍有不同，具体取决于你使用的是 SAS 证书还是 x.509 证书进行设备身份验证：

1. 配置连接设置：

    - **X.509 证书：** [添加并验证根/中间证书](#connect-devices-using-x509-certificates)，并在以下步骤中使用它来生成设备证书。
    - **SAS：** 复制主密钥。 此密钥是 IoT Central 应用程序的组 SAS 密钥。 在以下步骤中使用密钥生成设备 SAS 密钥。
    ![连接设置 SAS](media/overview-iot-central-get-connected-pnp/connection-settings-sas.png)

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

1. 已连接的设备最初显示为 "**设备**" 页上的 "未**关联**"。 设备预配状态为“已注册”。 将设备**迁移**到适当的设备模板，并批准设备以连接到 IoT Central 应用程序。 然后，设备可以从 IoT 中心检索连接字符串并开始发送数据。 现在已完成设备预配，预配状态为 "已**预**配"。

## <a name="individual-enrollment-based-device-connectivity"></a>基于单个注册的设备连接

对于连接设备，这些设备的身份验证凭据是每个设备的单个注册，就是选项。 单个注册是可能连接的单个设备的条目。 个人注册可使用 X.509 叶证书或 SAS 令牌（来自物理或虚拟 TPM）作为证明机制。 单个注册中的设备 ID （也称为 "注册 ID"）是字母数字、小写，可能包含连字符。在[此处](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)了解有关单独注册的详细信息。

> [!NOTE]
> 为设备创建单个注册时，其优先级高于应用中基于默认组注册证明（SAS、X509）的用户。

### <a name="creating-individual-enrollments"></a>创建单个注册
IoT Central 支持以下证明机制

1. **对称密钥证明：** 对称密钥证明是使用设备预配服务实例对设备进行身份验证的一种简单方法。 使用对称密钥创建单个注册;打开 "连接" 对话框，选择单个注册和机制 "SAS"，然后输入主密钥和辅助密钥。 SAS 密钥必须采用 base64 编码。 下面是指向代码示例的[链接](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample)，以帮助编写设备代码以使用对称密钥和单独的注册来设置设备。
1. **X.509 证书：** X.509 证书作为标题建议是一种基于证书的证明机制，是一种用于缩放生产的极佳方式。 若要使用对称密钥创建单个注册，请选择单个注册和机制 "x.509"，并上传主证书和辅助证书，并保存以创建注册。 下面是指向代码示例的[链接](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample)，以帮助编写设备代码以使用 X509 设置设备。 与[单个注册](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)条目一起使用的设备证书要求 "使用者名称" 必须设置为单个注册条目的设备 ID （也称为 "注册 ID"）。
1. **TPM 证明：** TPM 代表受信任的平台模块，是一种硬件安全模块（HSM），是连接设备的最安全方式之一。  本文假定你使用单独的、固件式的或集成式的 TPM。 软件模拟 TPM 适用于原型制作或测试，但其提供的安全级别不同于单独的、固件式的或集成式的 TPM。 建议不要在生产中使用软件 TPM。 若要创建使用对称密钥的单个注册，请选择单个注册和机制 "TPM" 并输入认可密钥以创建注册。有关 Tpm 类型的详细信息，可以在[此处](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)了解有关 TPM 证明的详细信息。 下面是指向代码示例的[链接](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/TpmSample)，可帮助使用 TPM 编写设备代码以设置设备。 若要创建基于 TPM 的证明，请键入签署密钥并保存。

## <a name="connect-devices-with-iot-plug-and-play"></a>将设备连接到 IoT 即插即用

具有 IoT Central 的 IoT 即插即用的主要功能之一是能够在设备连接上自动关联设备模板。 除了设备凭据，设备现在可以将**CapabilityModelId**作为设备注册呼叫的一部分发送，IoT Central 将发现并关联设备模板。 发现过程按以下顺序进行：

1. 如果已在 IoT Central 应用程序中发布，则与设备模板关联。
1. 从已发布和已认证的功能模型的公共存储库中获取。

下面是在 DPS 注册呼叫期间设备将发送的其他有效负载的格式

```javascript
'__iot:interfaces': {
              CapabilityModelId: <this is the URN for the capability model>
          }
```

> [!NOTE]
> 请注意，应启用自动批准选项，使设备能够自动连接，发现模型并开始发送数据。

## <a name="device-status"></a>设备状态

当真实设备连接到 IoT Central 应用程序时，其设备状态如下所示：

1. 首先**注册**设备状态。 此状态表示设备是在 IoT Central 中创建的，并且具有设备 ID。 设备在以下情况时注册：
    - 新的实际设备将添加到 "**设备**" 页上。
    - 使用 "**导入**" 在 "**设备**" 页上添加一组设备。

1. 当连接到具有有效凭据的 IoT Central 应用程序的设备完成预配步骤时，设备状态将更改为 "已**设置**"。 在此步骤中，设备将从 IoT 中心检索连接字符串。 设备现在可以连接到 IoT 中心并开始发送数据。

1. 操作员会阻止设备。 当某个设备被阻止时，它无法将数据发送到您的 IoT Central 应用程序。 被阻止的设备的状态为 "已**阻止**"。 操作员必须先重置设备，然后才能继续发送数据。 当操作员取消阻止设备时，该状态返回到**已注册或已** **设置**的以前值。

1. 设备状态为 "**正在等待批准**"，这意味着禁用了 "**自动批准**" 选项，并且需要操作员显式批准 IoT Central 连接到的所有设备。 未在 "**设备**" 页上手动注册的设备，但是使用有效凭据连接的设备的设备状态将**等待批准**。 操作员可以使用 "**批准**" 按钮，在 "**设备**" 页上批准这些设备。

1. 设备**状态为 "未关联"** ，这意味着连接到 IoT Central 的设备没有关联的设备模板。 这通常在以下情况下发生：
    - 使用 "**导入**" 在 "**设备**" 页上添加一组设备，而无需指定设备模板
    - 未在 "**设备**" 页上手动注册的设备与有效凭据连接，但在注册过程中未指定模板 ID。  
操作员可以使用 "**迁移**" 按钮，将设备从 "**设备**" 页关联到模板。

## <a name="sdk-support"></a>SDK 支持

Azure 设备 Sdk 为实现设备代码提供了最简单的方法。 以下设备 SDK 已发布：

- [适用于 C 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [适用于 Python 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [适用于 Node.js 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [适用于 Java 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [适用于 .NET 的 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 功能和 IoT 中心连接

与 IoT 中心进行的所有设备通信都使用以下 IoT 中心连接选项：

- [设备到云的消息传递](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [设备孪生](../../iot-hub/iot-hub-devguide-device-twins.md)

下表对 Azure IoT Central 设备功能映射到 IoT 中心功能的具体情况进行了汇总：

| Azure IoT 中心 | Azure IoT 中心 |
| ----------- | ------- |
| 度量：遥测 | 设备到云的消息传递 |
| 设备属性 | 设备孪生报告属性 |
| 设置 | 设备孪生所需的和报告的属性 |

若要了解有关使用设备 Sdk 的详细信息，请参阅[将 DevDiv 工具包设备连接到 Azure IoT Central 应用程序](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)，了解示例代码。

### <a name="protocols"></a>协议

设备 SDK 支持下述用于连接到 IoT 中心的网络协议：

- MQTT
- AMQP
- HTTPS

若要了解这些不同的协议以及选择协议的指南，请参阅[选择通信协议](../../iot-hub/iot-hub-devguide-protocols.md)。

如果设备无法使用这些受支持协议中的任何一种，可以使用 Azure IoT Edge 进行协议转换。 IoT Edge 支持其他边缘智能方案，可以将处理从 Azure IoT Central 应用程序卸载到边缘。

## <a name="security"></a>“安全”

在设备与 Azure IoT Central 之间交换的所有数据都经过加密。 如果设备已连接到任何面向设备的 IoT 中心终结点，则 IoT 中心会对从该设备发出的所有请求进行身份验证。 为了避免通过网络交换凭据，设备使用签名的令牌进行身份验证。 有关详细信息，请参阅[控制对 IoT 中心的访问](../../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>后续步骤

现在，你已了解 Azure IoT Central 中的设备连接，下面是建议的后续步骤：

- [准备和连接 DevKit 设备](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
- [C SDK：预配设备客户端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)
