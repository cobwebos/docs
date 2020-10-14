---
title: 管理设备证书 - Azure IoT Edge | Microsoft Docs
description: 创建测试证书，并在 Azure IoT Edge 设备上安装和管理证书，以准备生产部署。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1f07f9d481ca8ede29c8b8443dad81a442962a71
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044133"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>管理 IoT Edge 设备上的证书

所有 IoT Edge 设备使用证书在运行时与设备上运行的任何模块之间创建安全连接。 充当网关的 IoT Edge 设备也使用相同的证书连接到其下游设备。

## <a name="install-production-certificates"></a>安装生产证书

首次安装 IoT Edge 和预配设备时，将在设备上设置临时证书，使你能够测试服务。
这些临时证书将在 90 天后过期，重启计算机可以重置这些证书。
一旦转移到生产方案，或者想要创建网关设备，就需要提供自己的证书。
本文演示在 IoT Edge 设备上安装证书的步骤。

若要详细了解不同类型的证书及其角色，请参阅[了解 Azure IoT Edge 如何使用证书](iot-edge-certs.md)。

>[!NOTE]
>整篇文章中使用的术语“根 CA”是指 IoT 解决方案证书链的最顶层颁发机构公共证书。 不需要使用联合证书颁发机构的证书根，也不需要使用组织证书颁发机构的根。 在许多情况下，它实际上是中间 CA 公共证书。

### <a name="prerequisites"></a>先决条件

* IoT Edge 设备。

  如果未设置 IoT Edge 设备，则可以在 Azure 虚拟机中创建一个。 按照其中一篇快速入门文章中的步骤 [创建虚拟 Linux 设备](quickstart-linux.md) ，或 [创建虚拟 Windows 设备](quickstart.md)。

* 有一个根证书颁发机构 (CA) 证书，该证书是自签名证书，或者从 Baltimore、Verisign、DigiCert 或 GlobalSign 等可信商业证书颁发机构购买的证书。

  如果你没有根证书颁发机构，但想要试用需要生产证书的 IoT Edge 功能（例如网关方案），可以[创建演示证书来测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。

### <a name="create-production-certificates"></a>创建生产证书

应使用自己的证书颁发机构创建以下文件：

* 根 CA
* 设备 CA 证书
* 设备 CA 私钥

本文中所谓的“根 CA”并非组织的最顶层证书颁发机构。 它是 IoT Edge 方案的最顶层证书颁发机构，IoT Edge 中心模块、用户模块和任何下游设备使用该颁发机构来建立彼此之间的信任。

> [!NOTE]
> 目前存在一个 libiothsm 限制，会阻止使用在 2038 年 1 月 1 日或之后到期的证书。

若要查看这些证书的示例，请查看[管理用于示例和教程的测试 CA 证书](https://github.com/Azure/iotedge/tree/master/tools/CACertificates)中用于创建演示证书的脚本。

### <a name="install-certificates-on-the-device"></a>在设备上安装证书

在 IoT Edge 设备上安装证书链，并将 IoT Edge 运行时配置为引用新证书。

例如，如果使用示例脚本[创建了演示证书](how-to-create-test-certificates.md)，请将以下文件复制到 IoT-Edge 设备：

* 设备 CA 证书：`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* 设备 CA 私钥：`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* 根 CA：`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. 将三个证书和密钥文件复制到 IoT Edge 设备。

   可以使用 [Azure Key Vault](../key-vault/index.yml) 之类的服务或[安全复制协议](https://www.ssh.com/ssh/scp/)之类的功能来移动证书文件。  如果在 IoT Edge 设备本身上生成了证书，则可以跳过此步骤，并使用工作目录的路径。

1. 打开 IoT Edge 安全守护程序配置文件。

   * Windows： `C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

1. 将 config.yaml 文件中的 certificate 属性设置为 IoT Edge 设备上的证书和密钥文件的文件 URI 路径。 删除 certificate 属性前面的 `#` 字符，以取消注释四个代码行。 请确保 **certificates:** 行前面没有空格，并且嵌套项缩进了两个空格。 例如：

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * Linux：

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. 在 Linux 设备上，确保用户 **iotedge** 对保存证书的目录拥有读取权限。

1. 如果以前在设备上使用过 IoT Edge 的任何其他证书，请在启动或重启 IoT Edge 之前删除以下两个目录中的文件：

   * Windows：`C:\ProgramData\iotedge\hsm\certs` 和 `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux：`/var/lib/iotedge/hsm/certs` 和 `/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>自定义证书生存期

在多种情况下，IoT Edge 会在设备上自动生成证书，这些情况包括：

* 如果你在安装和预配 IoT Edge 时未提供自己的生产证书，则 IoT Edge 安全管理器会自动生成一个设备 CA 证书。 这个自签名证书仅用于开发和测试方案，而不可用于生产。 此证书在 90 天后过期。
* IoT Edge 安全管理器还会生成由设备 CA 证书签名的工作负荷 CA 证书

有关 IoT Edge 设备上不同证书的功能的详细信息，请参阅[了解 Azure IoT Edge 如何使用证书](iot-edge-certs.md)。

对于这两个自动生成的证书，可以选择在 config.yaml 中设置 auto_generated_ca_lifetime_days 标志，以配置证书生存期的天数。

>[!NOTE]
>IoT Edge 安全管理器还会创建第三个自动生成的证书：IoT Edge 中心服务器证书。 此证书的生存期始终为 90 天，但过期之前会自动续订。 auto_generated_ca_lifetime_days 值不会影响此证书。

若要将证书到期时间配置为默认的 90 天以外的时间，请在 config.yaml 文件的 certificates 节中添加所需值（以天为单位）。 

如果在指定的天数后到期，则必须重启 IoT Edge 安全守护程序以重新生成设备 CA 证书，该证书不会自动续订。

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

> [!NOTE]
> 目前存在一个 libiothsm 限制，会阻止使用在 2038 年 1 月 1 日或之后到期的证书。

在 config.yaml 文件中指定值后，请执行以下步骤：

1. 删除 `hsm` 文件夹的内容。

   Windows:`C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux：`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. 重启 IoT Edge 服务。

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux：

   ```bash
   sudo systemctl restart iotedge
   ```

1. 确认生存期设置。

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux：

   ```bash
   sudo iotedge check --verbose
   ```

   查看“生产就绪状态: 证书”检查的输出，其中列出了自动生成的设备 CA 证书在过期前的天数。

## <a name="next-steps"></a>后续步骤

在生产环境中部署解决方案之前，必须先在 IoT Edge 设备上安装证书。 详细了解如何[准备在生产环境中部署 IoT Edge 解决方案](production-checklist.md)。