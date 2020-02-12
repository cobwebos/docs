---
title: 在设备 Azure IoT Edge 上安装证书 |Microsoft Docs
description: 创建测试证书，并了解如何将其安装到 Azure IoT Edge 设备上，以便准备生产部署。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fe46e968aa2dcebaa483cd38fd2e050ccfe43054
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149892"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>在 IoT Edge 设备上安装生产证书

所有 IoT Edge 设备均使用证书在运行时与设备上运行的任何模块之间创建安全连接。
用作网关的 IoT Edge 设备也使用这些相同的证书连接到其下游设备。

首次安装 IoT Edge 和预配设备时，设备将设置为临时证书，以便你可以测试服务。
这些临时证书将在90天后过期，或者可以通过重新启动计算机进行重置。
准备好将设备移到生产方案中，或者想要创建网关方案后，需要提供自己的证书。
本文演示在 IoT Edge 设备上安装证书的步骤。

若要在 IoT Edge 方案中了解有关不同类型的证书及其角色的详细信息，请参阅[了解 Azure IoT Edge 使用证书的方式](iot-edge-certs.md)。

>[!NOTE]
>本文中使用的 "根 CA" 一词是指 IoT 解决方案的证书链的最顶层颁发机构公共证书。 不需要使用联合的证书颁发机构的证书根，也不需要使用组织的证书颁发机构的根。 在许多情况下，它实际上是一个中间 CA 公共证书。

## <a name="prerequisites"></a>必备条件

* IoT Edge 设备，在[Windows](how-to-install-iot-edge-windows.md)或[Linux](how-to-install-iot-edge-linux.md)上运行。
* 具有根证书颁发机构（CA）证书（自签名）或从巴尔的摩、Verisign、DigiCert 或 GlobalSign 等可信商业证书颁发机构购买的证书。

如果还没有根证书颁发机构，但想要尝试 IoT Edge 需要生产证书的功能（如网关方案），可以[创建演示证书来测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。

## <a name="create-production-certificates"></a>创建生产证书

应使用自己的证书颁发机构创建以下文件：

* 根 CA
* 设备 CA 证书
* 设备 CA 私钥

本文所指的是，*根 CA*不是组织的最顶层证书颁发机构。 这是 IoT Edge 方案最顶层的证书颁发机构，IoT Edge 中心模块、用户模块和任何下游设备使用它们来建立彼此之间的信任。

若要查看这些证书的示例，请查看在[管理示例和教程的测试 CA 证书](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)中创建演示证书的脚本。

## <a name="install-certificates-on-the-device"></a>在设备上安装证书

在 IoT Edge 设备上安装证书链，并将 IoT Edge 运行时配置为引用新证书。

例如，如果使用示例脚本[创建演示证书](how-to-create-test-certificates.md)，则需要复制到 IoT Edge 设备上的三个文件如下所示：

* 设备 CA 证书： `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 设备 CA 私钥： `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 根 CA： `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 将三个证书和密钥文件复制到 IoT Edge 设备上。

   你可以使用诸如[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)之类的服务或类似于[安全复制协议](https://www.ssh.com/ssh/scp/)的函数来移动证书文件。  如果在 IoT Edge 设备本身生成了证书，则可以跳过此步骤并使用工作目录的路径。

2. 打开 IoT Edge 安全守护程序配置文件。

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 将 yaml 文件中的**证书**属性设置为 IoT Edge 设备上的证书和密钥文件的文件 URI。 删除证书属性之前的 `#` 字符，取消注释四行。 请确保**证书：** 行前面没有空格，并且嵌套项按两个空格缩进。 例如：

   * Windows：

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux：

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. 在 Linux 设备上，确保用户**iotedge**具有保存证书的目录的读取权限。

5. 如果以前在设备上使用了任何其他证书用于 IoT Edge，请在启动或重新启动 IoT Edge 之前，删除以下两个目录中的文件：

   * Windows： `C:\ProgramData\iotedge\hsm\certs` 和 `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux： `/var/lib/iotedge/hsm/certs` 和 `/var/lib/iotedge/hsm/cert_keys`

## <a name="next-steps"></a>后续步骤

在生产环境中部署解决方案之前，必须先在 IoT Edge 设备上安装证书。 了解有关如何[准备在生产中部署 IoT Edge 解决方案的](production-checklist.md)详细信息。
