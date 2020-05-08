---
title: Azure IoT Central 中的设备连接 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 中的设备连接相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ddbb1c6fd705e658867c0d594981e87bc8cd6afe
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930482"
---
# <a name="get-connected-to-azure-iot-central"></a>连接到 Azure IoT Central

*本文适用于操作员和设备开发人员。*

本文介绍用于将设备连接到 Azure IoT Central 应用程序的选项。

通常，必须先在应用程序中注册设备，然后才能进行连接。 但是，IoT Central 支持在[没有首先注册的情况下可以连接设备](#connect-without-registering-devices)的方案。

IoT Central 使用[Azure IoT 中心设备预配服务（DPS）](../../iot-dps/about-iot-dps.md)来管理连接过程。 设备首先连接到 DPS 终结点，以检索连接到应用程序所需的信息。 IoT Central 应用程序在内部使用 IoT 中心来处理设备连接。 使用 DPS：

- 可让 IoT Central 支持大规模的设备加入和连接。
- 可让你离线生成设备凭据并配置设备，而无需通过 IoT Central UI 注册设备。
- 可让你使用自己的设备 ID 在 IoT Central 中注册设备。 使用自己的设备 ID 可以简化与现有后端办公系统的集成。
- 可以通过一致的单一方式将设备连接到 IoT Central。

为了保护设备与应用程序之间的通信，IoT Central 支持共享访问签名（SAS）和 x.509 证书。 在生产环境中，建议使用 x.509 证书。

本文介绍以下用例：

- [使用 SAS 连接单个设备](#connect-a-single-device)
- [使用 SAS 大规模连接设备](#connect-devices-at-scale-using-sas)
- [使用 x.509 证书大规模连接设备](#connect-devices-using-x509-certificates)-建议用于生产环境的方法。
- [连接设备而不先注册设备](#connect-without-registering-devices)
- [连接使用 DPS 单个注册的设备](#individual-enrollment-based-device-connectivity)
- [自动将设备与设备模板关联](#automatically-associate-with-a-device-template)

## <a name="connect-a-single-device"></a>连接单个设备

在体验 IoT Central 或测试设备时，此方法非常有用。 你可以使用 IoT Central 应用程序中的设备连接 SAS 密钥将设备连接到 IoT Central 应用程序。 从已注册设备的连接信息中复制_设备 SAS 密钥_：

![单个设备的 SAS 密钥](./media/concepts-get-connected/single-device-sas.png)

若要了解详细信息，请参阅[创建 node.js 客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device-nodejs.md)教程。

## <a name="connect-devices-at-scale-using-sas"></a>使用 SAS 大规模连接设备

若要使用 SAS 密钥将设备连接到 IoT Central 大规模，需注册并设置设备：

### <a name="register-devices-in-bulk"></a>批量注册设备

若要将大量设备注册到 IoT Central 应用程序，请使用 CSV 文件[导入设备 ID 和设备名称](howto-manage-devices.md#import-devices)。

若要检索已导入的设备的连接信息，请[从 IoT Central 应用程序导出 CSV 文件](howto-manage-devices.md#export-devices)。 导出的 CSV 文件包括设备 Id 和 SAS 密钥。

### <a name="set-up-your-devices"></a>设置设备

在设备代码中使用导出文件中的连接信息，使设备能够连接到 IoT Central 应用程序并向其发送数据。 还需要为应用程序提供 DPS **ID 范围**。 你可以在 "管理" **> 设备连接**"中找到此值。

> [!NOTE]
> 若要了解如何在不事先将设备注册到 IoT Central 的情况下连接设备，请参阅[在不事先注册设备的情况下连接设备](#connect-without-registering-devices)。

## <a name="connect-devices-using-x509-certificates"></a>使用 X.509 证书连接设备

在生产环境中，建议使用 X.509 证书作为 IoT Central 的设备身份验证机制。 有关详细信息，请参阅[使用 X.509 CA 证书进行设备身份验证](../../iot-hub/iot-hub-x509ca-overview.md)。

在使用 x.509 证书连接设备之前，请在应用程序中添加并验证中间或根 x.509 证书。 设备必须使用从根或中间证书生成的叶 x.509 证书。

### <a name="add-and-verify-a-root-or-intermediate-certificate"></a>添加并验证根或中间证书

导航到 "**管理" > 设备连接 "> 管理主证书**，并添加用于生成设备证书的 x.509 根证书或中间证书。

![连接设置](media/concepts-get-connected/manage-x509-certificate.png)

验证证书所有权可确保上传证书的人员具有证书的私钥。 若要验证证书：

  1. 选择“验证码”旁边的按钮以生成代码。****
  1. 使用在上一步骤中生成的验证码创建 X.509 验证证书。 将该证书保存为 .cer 文件。
  1. 上传已签名的验证证书，然后选择“验证”。**** 验证成功后，证书将标记为 "**已验证**"。

如果存在安全漏洞或者主要证书设置为会过期，请使用辅助证书来减少停机时间。 在更新主要证书时，可以继续使用辅助证书来预配设备。

### <a name="register-and-connect-devices"></a>注册并连接设备

若要使用 x.509 证书批量连接设备，请先在应用程序中注册设备，方法是使用 CSV 文件[导入设备 id 和设备名称](howto-manage-devices.md#import-devices)。 设备 Id 应该全部为小写。

使用上传的根证书或中间证书生成设备的 x.509 叶证书。 使用**设备 ID**作为叶证书`CNAME`中的值。 设备代码需要应用程序的**ID 范围**值、**设备 ID**和相应的设备证书。

#### <a name="sample-device-code"></a>示例设备代码

以下来自[Azure IoT NODE.JS SDK](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/device/samples/register_x509.js)的示例显示 node.js 设备客户端如何使用 x.509 叶证书和 DPS 注册到 IoT Central 应用程序：

:::code language="nodejs" source="~/azure-iot-sdk-node/provisioning/device/samples/register_x509.js":::

有关等效的 C 示例，请参阅[Azure IoT C 预配设备客户端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_provisioning_client.md)中的[prov_dev_client_sample。](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c)

### <a name="for-testing-purposes-only"></a>仅用于测试目的

仅适用于测试，你可以使用以下实用工具生成根证书、中间证书和设备证书：

- [适用于 Azure IoT 设备预配设备 SDK 的工具](https://github.com/Azure/azure-iot-sdk-node/blob/master/provisioning/tools/readme.md)：可用于生成和验证 x.509 证书和密钥的 node.js 工具的集合。
- 如果使用 DevKit 设备，则此[命令行工具](https://aka.ms/iotcentral-docs-dicetool)会生成一个 CA 证书，可将其添加到 IoT Central 应用程序来验证证书。
- [管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)： PowerShell 和 Bash 脚本的集合：
  - 创建证书链。
  - 将证书保存为 .cer 文件，以上传到 IoT Central 应用程序。
  - 使用 IoT Central 应用程序中的验证码生成验证证书。
  - 在工具中使用设备 ID 作为参数，为设备创建叶证书。

## <a name="connect-without-registering-devices"></a>在不事先注册设备的情况下连接设备

上述方案都要求您在连接之前在应用程序中注册设备。 IoT Central 还使 Oem 能够批量制造可以连接的设备，而无需先进行注册。 OEM 生成合适的设备凭据，并在工厂中配置设备。 当客户首次打开设备时，它将连接到 DPS，然后将设备自动连接到正确的 IoT Central 应用程序。 IoT Central 操作员必须先批准设备，然后才能开始向应用程序发送数据。

此流程略有不同，具体取决于设备是使用 SAS 令牌还是使用 x.509 证书：

### <a name="connect-devices-that-use-sas-tokens-without-registering"></a>连接使用 SAS 令牌的设备而无需注册

1. 复制 IoT Central 应用程序的组主密钥：

    ![应用程序组主 SAS 密钥](media/concepts-get-connected/group-sas-keys.png)

1. 使用[dps-ssh-keygen](https://www.npmjs.com/package/dps-keygen)工具生成设备 SAS 密钥。 使用上一步中的 "组主密钥"。 设备 Id 必须是小写的：

    ```cmd
    dps-keygen -mk:<group primary key> -di:<device ID>
    ```

1. OEM 用设备 ID、生成的设备 SAS 密钥和应用程序**ID 范围**值闪烁每个设备。

1. 打开设备时，它首先会连接到 DPS 以检索其 IoT Central 注册信息。

    设备最初在 "**设备**" 页上具有 "未**关联**设备" 状态，并且未分配给设备模板。 在 "**设备**" 页上，将设备**迁移**到适当的设备模板。 设备预配现已完成，设备状态现在为 "已**设置**"，设备可以开始发送数据。

    在 "**管理 > 设备连接**" 页上，"**自动批准**" 选项控制是否需要在设备开始发送数据之前手动批准。

    > [!NOTE]
    > 若要了解如何自动将设备与设备模板关联，请参阅[自动将设备与设备模板关联](#automatically-associate-with-a-device-template)。

### <a name="connect-devices-that-use-x509-certificates-without-registering"></a>连接使用 x.509 证书的设备而不注册

1. [将根或中间 x.509 证书添加](#connect-devices-using-x509-certificates)到 IoT Central 应用程序并进行验证。

1. 使用已添加到 IoT Central 应用程序的根证书或中间证书为设备生成叶证书。 使用小写的设备 Id 作为叶证书`CNAME`中的。

1. OEM 用设备 ID、生成的叶 x.509 证书和应用程序**ID 范围**值闪烁每个设备。

1. 打开设备时，它首先会连接到 DPS 以检索其 IoT Central 注册信息。

    设备最初在 "**设备**" 页上具有 "未**关联**设备" 状态，并且未分配给设备模板。 在 "**设备**" 页上，将设备**迁移**到适当的设备模板。 设备预配现已完成，设备状态现在为 "已**设置**"，设备可以开始发送数据。

    在 "**管理 > 设备连接**" 页上，"**自动批准**" 选项控制是否需要在设备开始发送数据之前手动批准。

    > [!NOTE]
    > 若要了解如何自动将设备与设备模板关联，请参阅[自动将设备与设备模板关联](#automatically-associate-with-a-device-template)。

## <a name="individual-enrollment-based-device-connectivity"></a>基于注册的单个设备连接

对于连接设备的每个设备都有自己的身份验证凭据，请使用单独的注册。 单个注册是允许连接的单个设备的条目。 个人注册可使用 x.509 叶证书或 SAS 令牌（来自物理或虚拟受信任的平台模块）作为证明机制。 单个注册中的设备 ID （也称为 "注册 ID"）是字母数字、小写形式，可以包含连字符。 有关详细信息，请参阅[DPS 单个注册](https://docs.microsoft.com/azure/iot-dps/concepts-service#individual-enrollment)。

> [!NOTE]
> 为设备创建单个注册时，其优先级高于 IoT Central 应用程序中的默认组注册选项。

### <a name="create-individual-enrollments"></a>创建单个注册

IoT Central 支持用于单个注册的以下证明机制：

- **对称密钥证明：** 对称密钥证明是使用 DPS 实例对设备进行身份验证的一种简单方法。 若要创建使用对称密钥的单个注册，请打开 "**设备连接**" 页，选择 "**单个注册**" 作为 "连接方法"，并选择 "**共享访问签名（SAS）** " 作为机制。 输入 base64 编码的主密钥和辅助密钥，并保存所做的更改。 使用**id 范围**、**设备 ID**以及主密钥或辅助密钥来连接设备。

    > [!TIP]
    > 对于测试，可以使用**OpenSSL**生成 base64 编码的密钥：`openssl rand -base64 64`

- **X.509 证书：** 若要使用 x.509 证书创建单个注册，请打开 "**设备连接**" 页，选择 "**单个注册**" 作为连接方法，然后选择 "**证书" （x.509）** 作为机制。 与单个注册条目一起使用的设备证书要求将颁发者和使用者 CN 设置为设备 ID。

    > [!TIP]
    > 对于测试，可以使用适用于 node.js 的[Azure IoT 设备预配设备 SDK 工具](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)来生成自签名证书：`node create_test_cert.js device "mytestdevice"`

- **受信任的平台模块（TPM）证明：**[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation)是一种硬件安全模块。 使用 TPM 是连接设备的最安全方式之一。 本文假设使用的是独立的、固件或集成的 TPM。 软件仿真 Tpm 非常适合用于原型制作或测试，但它们不提供与离散、固件或集成 Tpm 相同的安全性级别。 请勿在生产环境中使用软件 Tpm。 若要创建使用 TPM 的单个注册，请打开 "**设备连接**" 页，选择 "**单个注册**" 作为 "连接方法"，选择 " **TPM** " 作为机制。 输入 TPM 认可密钥并保存设备连接信息。

## <a name="automatically-associate-with-a-device-template"></a>自动与设备模板关联

IoT Central 的主要功能之一是能够在设备连接上自动关联设备模板。 设备还可以将**CapabilityModelId**作为设备注册呼叫的一部分发送。 **CapabilityModelID**是用于标识设备实现的功能模型的 URN。 IoT Central 应用程序可以使用**CapabilityModelID**来标识要使用的设备模板，然后自动将设备与设备模板关联。 发现过程的工作方式如下：

1. 如果设备模板已在 IoT Central 应用程序中发布，则设备将与设备模板关联。
1. 对于预认证的 IoT 即插即用设备，如果设备模板尚未在 IoT Central 应用程序中发布，则从公共存储库中获取设备模板。

以下代码片段显示了在 DPS 注册呼叫过程中设备必须发送的其他有效负载的格式，以使自动关联能够正常工作。

这是使用不支持 IoT 即插即用的已正式提供设备 SDK 的设备的格式：

```javascript
    iotcModelId: '< this is the URN for the capability model>';
```

这是使用公共预览版设备 SDK 但支持 IoT 即插即用的设备的格式：

```javascript
'__iot:interfaces': {
    CapabilityModelId: <this is the URN for the capability model>
}
```

> [!NOTE]
> 若要使设备自动连接，请启用 "**管理 >** 的**自动批准**" 选项，然后发现设备模板并开始发送数据。

## <a name="device-status-values"></a>设备状态值

当真实设备连接到 IoT Central 应用程序时，其设备状态将按如下所述发生更改：

1. 设备状态最初为“已注册”。**** 此状态表示该设备已在 IoT Central 中创建，并具有设备 ID。 出现以下情况时，设备处于已注册状态：
    - 在“设备”页上添加新的真实设备。****
    - 在“设备”页上使用“导入”添加一组设备。********

1. 当已使用有效凭据连接到 IoT Central 应用程序的设备完成预配步骤时，设备状态将更改为“已预配”。**** 在此步骤中，设备使用 DPS 自动从 IoT Central 应用程序所使用的 IoT 中心检索连接字符串。 设备现在可以连接到 IoT Central 并开始发送数据。

1. 操作员可以阻止设备。 阻止设备时，它无法将数据发送到 IoT Central 应用程序。 已阻止的设备的状态为“已阻止”。**** 只有在操作员重置设备后，该设备才能继续发送数据。 当操作员取消阻止设备时，状态将恢复为以前的值：“已注册”或“已预配”。********

1. 如果设备状态为 "**正在等待批准**"，则表示 "**自动批准**" 选项处于禁用状态。 操作员必须先显式批准设备，然后才能开始发送数据。 未在“设备”页上手动注册，但已使用有效凭据建立连接的设备的状态将是“等待批准”。******** 操作员可以在“设备”页中使用“批准”按钮批准这些设备。********

1. 如果设备状态为 "未**关联**"，则表示连接到 IoT Central 的设备没有关联的设备模板。 这种情况通常发生在以下情况下：

    - 使用 "**导入**" 在 "**设备**" 页上添加一组设备，而无需指定设备模板。
    - 设备已在 "**设备**" 页上手动注册，无需指定设备模板。 然后，设备会连接到有效的凭据。  

    操作员可以使用 "**迁移**" 按钮，将设备从 "**设备**" 页关联到设备模板。

## <a name="best-practices"></a>最佳做法

第一次连接设备时，请勿保留或缓存 DPS 返回的设备连接字符串。 若要重新连接设备，请执行标准设备注册流以获取正确的设备连接字符串。 如果设备缓存连接字符串，则在 IoT Central 更新其使用的基础 Azure IoT 中心时，设备软件面临具有陈旧连接字符串的风险。

## <a name="sdk-support"></a>SDK 支持

Azure 设备 SDK 为实现设备代码提供最简便的方法。 以下设备 SDK 已发布：

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

| Azure IoT Central | Azure IoT 中心 |
| ----------- | ------- |
| 遥测 | 设备到云的消息传递 |
| 属性 | 设备孪生报告属性 |
| 属性（可写） | 设备孪生所需的和报告的属性 |
| Command | 直接方法 |

若要详细了解如何使用设备 SDK，请参阅[将 DevDiv 工具包设备连接到 Azure IoT Central 应用程序](howto-connect-devkit.md)中的示例代码。

### <a name="protocols"></a>协议

设备 SDK 支持下述用于连接到 IoT 中心的网络协议：

- MQTT
- AMQP
- HTTPS

若要了解这些不同的协议以及选择协议的指南，请参阅[选择通信协议](../../iot-hub/iot-hub-devguide-protocols.md)。

如果设备无法使用这些受支持协议中的任何一种，可以使用 Azure IoT Edge 进行协议转换。 IoT Edge 支持其他边缘智能方案，可以将处理从 Azure IoT Central 应用程序卸载到边缘。

## <a name="security"></a>安全性

在设备与 Azure IoT Central 之间交换的所有数据都经过加密。 如果设备已连接到任何面向设备的 IoT 中心终结点，则 IoT 中心会对从该设备发出的所有请求进行身份验证。 为了避免通过网络交换凭据，设备使用签名的令牌进行身份验证。 有关详细信息，请参阅[控制对 IoT 中心的访问](../../iot-hub/iot-hub-devguide-security.md)。

## <a name="next-steps"></a>后续步骤

如果你是一名设备开发人员，我们建议执行以下后续步骤：

- 了解如何[使用 Azure CLI 监视设备连接](./howto-monitor-devices-azure-cli.md)
- 了解如何[在 Azure IoT Central 应用程序中定义新的 IoT 设备类型](./howto-set-up-template.md)
- 了解[Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)
