---
title: 使用 Azure IoT 中心设备预配服务 SDK 中提供的工具简化开发
description: 本文档介绍如何使用 Azure IoT 中心设备预配服务 SDK 中提供的工具进行开发
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: 647f54d8252c594a280f81d661a3de6270bf692b
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001341"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>如何使用 SDK 中提供的工具来简化预配开发
IoT 中心设备预配服务简化了预配过程，以安全且可缩放的方式实现零接触实时[自动预配](concepts-auto-provisioning.md)。  需要 X.509 证书形式的安全证明，或受信任的平台模块 (TPM)。  Microsoft 还与[其他安全硬件合作伙伴](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)携手合作，共同提高安全 IoT 部署的可信度。 对于开发者，了解硬件安全需求可能是一项大挑战。 我们提供了一组 Azure IoT 预配服务 SDK，因此，开发者可使用一个便捷的层来编写与预配服务通信的客户端。 这些 SDK 还提供了常用方案的示例，以及一组可简化开发安全证明的工具。

## <a name="trusted-platform-module-tpm-simulator"></a>受信任的平台模块 (TPM) 模拟器
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security#trusted-platform-module-tpm) 可引用用于对平台进行身份验证的安全存储密钥的标准，也可引用与用于实现该标准的模块交互的 I/O 接口。 TPM 可以作为离散硬件、集成硬件、基于固件或基于软件的方式存在。  在生产中，TPM 位于设备上，作为基于离散硬件、集成硬件或固件的模块。 在测试阶段，向开发者提供基于软件的 TPM 模拟器。  当前，此模拟器仅适用于在 Windows 平台上进行开发。

使用 TPM 模拟器的步骤如下：
1. [准备开发环境](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment)并克隆 GitHub 存储库：
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. 导航到 ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/``` 下的 TPM 模拟器文件夹。
3. 先运行 Simulator.exe，再运行任何客户端应用程序进行设备预配。
4. 在整个预配过程中，在后台运行模拟器，以获取注册 ID 和认可密钥。  这两个值只对运行的一个实例有效。

## <a name="x509-certificate-generator"></a>X.509 证书生成器
可将 [X.509 证书](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates)用作一种证明机制，用于缩放生产规模和简化设备预配。  可通过[多种方式](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate)获取 X.509 证书：
* 对于生产环境，建议从公共根证书颁发机构购买 X.509 CA 证书。
* 对于测试环境，可通过以下方法生成 X.509 根证书或 X.509 证书链：
    * OpenSSL：可以使用脚本生成证书：
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell 或 Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * 设备标识组合引擎 (DICE) 仿真器：DICE 可基于 TLS 协议和 X.509 客户端证书用于加密设备标识和证明。  [了解](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/)有关使用 DICE 的设备标识的详细信息。

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>使用带 DICE 仿真器的 X.509 证书生成器
SDK 提供带 DICE 仿真器的 X.509 证书生成器，位于 [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator) 中。  此生成器可跨平台运行。  生成的证书可用于以其他语言进行开发。

当前，DICE 仿真器可输出根证书、中间证书、分支证书和关联的私钥。  但根证书或中间证书不可用于对单独的分支证书签名。  如果想测试组注册方案（其中，使用一个签名证书对多个设备的分支证书签名），可使用 OpenSSL 生成证书链。

使用此生成器生成 X.509 证书：
1. [准备开发环境](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java#prepare-the-development-environment)并克隆 GitHub 存储库：
```
git clone https://github.com/Azure/azure-iot-sdk-java.git
```
2. 将根目录更改为 azure-iot-sdk-java。
3. 运行 ```mvn install -DskipTests=true```，下载所需的所有包，并编译 SDK
4. 导航到 ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator``` 中 X.509 证书生成器的根目录。
5. 使用 ```mvn clean install``` 生成
6. 使用以下命令来运行工具：
```
cd target
java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
```
7. 出现提示时，可以选择性地为证书输入“公用名称”。
8. 此工具在本地生成“客户端证书”、“客户端证书私钥”、“中间证书”和“根证书”。

“客户端证书”是设备上的分支证书。  设备客户端需要“客户端证书”和关联的“客户端证书私钥”。 根据所选语言，客户端应用程序放置这些内容的机制可能有所不同。  有关详细信息，请参阅有关如何使用 X.509 创建模拟设备的[快速入门](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509)。

根证书或中间证书可用于[以编程方式](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks)或通过[门户](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments)创建注册组或单个注册。

## <a name="next-steps"></a>后续步骤
* 使用适用于 Azure IoT 中心和 Azure IoT 中心设备预配服务的 [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks)进行开发