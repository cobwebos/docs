---
title: 设备安全性证书 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 使用证书来验证设备、模块和叶节点设备，并在它们之间建立安全连接。
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: 1b299cf21652c23451aed735b10597adb85dc3db
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982722"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>了解 Azure IoT Edge 使用证书的方式

IoT Edge 证书由模块和下游 IoT 设备用来验证 [IoT Edge 中心](iot-edge-runtime.md#iot-edge-hub)运行时模块的身份和合法性。 这些验证可实现运行时、模块和 IoT 设备之间的 TLS（传输层安全性）安全连接。 与 IoT 中心本身一样，IoT Edge 需要来自 IoT 下游（或叶）设备和 IoT Edge 模块的安全加密连接。 为了建立安全的 TLS 连接，IoT Edge 中心模块为连接客户端提供服务器证书链，以便它们验证其身份。

本文介绍了 IoT Edge 证书如何在生产、开发和测试方案中工作。 虽然脚本不同（Powershell 与 bash），但 Linux 和 Windows 之间的概念是相同的。

## <a name="iot-edge-certificates"></a>IoT Edge 证书

通常，制造商不是 IoT Edge 设备的最终用户。 有时，两者之间的唯一关系是最终用户或操作员购买制造商生产的通用设备。 其他时候，制造商根据合同为运营商构建自定义设备。 IoT Edge 证书设计尝试考虑这两种情况。

下图说明了 IoT Edge 证书使用情况。 根 CA 证书和设备 CA 证书之间可能存在零个、一个或多个中间签名证书，具体取决于所涉及的实体数量。 下面介绍一个用例。

![典型证书关系图](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>证书颁发机构

证书颁发机构（简称“CA”）是颁发数字证书的实体。 证书颁发机构充当所有者和证书接收者之间的可信第三方。 数字证书可认证证书接收者对公钥的所有权。 使用证书信任链的方式是先颁发根证书，这是信任机构颁发的所有证书的基础。 之后，所有者可使用根证书颁发其他中间证书（“叶”证书）。

### <a name="root-ca-certificate"></a>根 CA 证书

根 CA 证书是整个过程的信任根。 在生产方案中，此 CA 证书通常从受信任的商业证书颁发机构（如 Baltimore、Verisign 或 DigiCert）购买。 如果可完全控制连接到 IoT Edge 设备的设备，即可使用公司级证书颁发机构。 不管什么情况，IoT Edge 中心的整个证书链都会将信息汇总到其中，因此叶 IoT 设备必须信任根证书。 可将根 CA 证书存储在受信任的根证书颁发机构存储中，也可以在应用程序代码中提供证书详细信息。

### <a name="intermediate-certificates"></a>中间证书

在创建安全设备的典型制造过程中，很少直接使用根 CA 证书，这主要是因为存在泄漏或暴露的风险。 根 CA 证书创建并对一个或多个中间 CA 证书进行数字签名。 可能只有一个中间证书，也可能存在中间证书链。 需要中间证书链的情景包括：

* 制造商各部门的层次结构。

* 多家公司连续参与生产设备。

* 客户购买根 CA 并派生签名证书，以便制造商代表该客户对制造的设备签名。

在任何情况下，制造商都使用此链末尾的中间 CA 证书来对终端设备上的设备 CA 证书进行签名。 一般情况下，这些中间证书受到制造厂商的严密保护。 这些证书的使用需要遵循严格的物理和电子流程。

### <a name="device-ca-certificate"></a>设备 CA 证书

设备 CA 证书由流程中的最终中间 CA 证书生成并签名。 此证书安装在 IoT Edge 设备上，最好安装在硬件安全模块 (HSM) 的安全存储中。 此外，设备 CA 证书可唯一标识 IoT Edge 设备。 设备 CA 证书可以为其他证书签名。

### <a name="iot-edge-workload-ca"></a>IoT Edge 工作负载 CA

IoT Edge 首次启动时，[IoT Edge 安全管理器](iot-edge-security-manager.md)会生成工作负载 CA 证书，这是流程“操作员”端的第一个证书。 此证书由“设备 CA 证书”生成并签名。 此证书只是另一个中间签名证书，用于生成 IoT Edge 运行时使用的任何其他证书并对其签名。 今天，下一节主要讨论 IoT Edge 中心服务器证书，但将来可能涵盖用于对 IoT Edge 组件进行身份验证的其他证书。

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge 中心服务器证书

IoT Edge 中心服务器证书是向设备和模块提供的实际证书，用于在建立 IoT Edge 所需的 TLS 连接期间进行身份验证。 此证书提供完整的签名证书链，用于将其生成到叶 IoT 设备必须信任的根 CA 证书。 由 IoT Edge 安全管理器生成时，此 IoT Edge 中心证书的公用名 (CN) 在转换为小写后将设置为 config.yaml 文件中的“hostname”属性。 此配置是与 IoT Edge 混淆的常见根源。

## <a name="production-implications"></a>生产影响

一个合理的问题可能是“为什么 IoT Edge 需要‘工作负载 CA’额外的证书？ 它无法使用设备 CA 证书直接生成 IoT Edge 中心服务器证书吗？”。 从技术上讲，它可以。 但是，此“工作负载”中间证书的目的是分离设备制造商和设备操作员之间的关注点。 想象一个场景，销售 IoT Edge 设备或将其从一个客户或转移给另一个客户。 你可能希望制造商提供的设备 CA 证书是不可变的。 但是，应该擦除并重新创建特定于设备操作的“工作负载”证书以用于新部署。

由于生产和操作过程是分开的，因此在准备生产设备时请考虑以下含义：

* 对于任何基于证书的流程，在推出 IoT Edge 设备的整个过程中，应保护和监视根 CA 证书和所有中间 CA 证书。 IoT Edge 设备制造商应制定可靠的流程来正确存储和使用其中间证书。 此外，设备 CA 证书应存储在设备上尽可能安全的存储中，最好是硬件安全模块。

* IoT Edge 中心服务器证书由 IoT Edge 中心提供给连接的客户端设备和模块。 设备 CA 证书的公用名 (CN) 不得与将在 IoT Edge 设备上 config.yaml 中使用的“主机名”相同。 客户端用于连接到 IoT Edge 的名称（例如，通过连接字符串的 GatewayHostName 参数或 MQTT 中的 CONNECT 命令使用的名称）“不得”与设备 CA 证书中使用的公用名相同。 此限制是因为 IoT Edge 中心提供其整个证书链以供客户端验证。 若 IoT Edge 中心服务器证书和设备 CA 证书具有相同的 CN，则会进入验证循环，证书将失效。

* 由于 IoT Edge 安全守护程序使用设备 CA 证书生成最终的 IoT Edge 证书，因此它本身必须是签名证书，这意味着它具有证书签名功能。 将“V3 基本约束 CA:True”应用于设备 CA 证书可自动设置所需的密钥用法属性。

>[!Tip]
> 若已使用我们的“便利脚本”（参阅下一节）在开发/测试方案中将 IoT Edge 设置为透明网关，并在创建设备 CA 证书时使用与 config.yaml 中的主机名相同的主机名，你可能想知道它的工作原理。 为了简化开发人员体验，便利脚本在传递给脚本的名称末尾追加“.ca”。 因此，若使用“mygateway”作为脚本中的设备名称和 config.yaml 中的主机名，则在用作设备 CA 证书的 CN 之前，前者将转换为 mygateway.ca。

## <a name="devtest-implications"></a>开发/测试影响

为简化开发和测试方案，Microsoft 提供了一组[便利脚本](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)，用于在透明网关方案中生成适用于 IoT Edge 的非生产证书。 有关脚本工作方式的示例，请参阅[创建演示证书以测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。

>[!Tip]
> 要通过 IoT Edge 连接设备 IoT“叶”设备和使用 IoT 设备 SDK 的应用程序，必须将可选的 GatewayHostName 参数添加到设备连接字符串的末尾。 生成 Edge 中心服务器证书时，该证书基于 config.yaml 中主机名的小写版本，因此，为使要匹配的名称和 TLS 证书验证成功，应以小写形式输入 GatewayHostName 参数。

## <a name="example-of-iot-edge-certificate-hierarchy"></a>IoT Edge 证书层次结构示例

为举例说明此证书路径，以下屏幕截图来自正在运行的设置为透明网关的 IoT Edge 设备。 OpenSSL 用于连接到 IoT Edge 中心，验证和转储证书。

![每个级别的证书层次结构的屏幕截图](./media/iot-edge-certs/iotedge-cert-chain.png)

可以在屏幕截图中看到证书深度的层次结构：

| 根 CA 证书         | 仅限 Azure IoT 中心 CA 证书测试                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| 中间 CA 证书 | 仅限 Azure IoT 中心中间证书测试                                                                 |
| 设备 CA 证书       | iotgateway.ca（将“iotgateway”作为 <网关主机名> 传递给便利脚本）   |
| 工作负载 CA 证书     | iotedge workload ca                                                                                       |
| IoT Edge 中心服务器证书 | iotedgegw.local（与 config.yaml 中的“主机名”匹配）                                            |

## <a name="next-steps"></a>后续步骤

[了解 Azure IoT Edge 模块](iot-edge-modules.md)

[配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
