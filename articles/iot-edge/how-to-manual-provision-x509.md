---
title: 预配 x.509 证书-Azure IoT Edge |Microsoft Docs
description: 安装后，使用设备标识证书预配 IoT Edge 设备，并向 IoT 中心进行身份验证
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 8cfb7c5a0821bd030252a105b98b1c138b9ef820
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979440"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>使用 x.509 证书身份验证设置 Azure IoT Edge 设备

本文提供在 IoT 中心注册新 IoT Edge 设备并将设备配置为使用 x.509 证书进行身份验证的步骤。

本文中的步骤逐步讲解称为手动设置的过程，在此过程中，将每个设备手动连接到其 IoT 中心。 另一种方法是使用 IoT 中心设备预配服务进行自动预配，在有许多设备进行预配时，这会很有用。

<!--TODO: Add auto-provision info/links-->

对于手动设置，您可以使用两个选项来对 IoT Edge 设备进行身份验证：

* **对称密钥**：在 IoT 中心创建新设备标识时，服务将创建两个密钥。 将其中一个密钥放置在设备上，并在进行身份验证时向 IoT 中心提供密钥。

  此身份验证方法的启动速度更快，但并不安全。

* **X.509 自签名**：创建两个 x.509 标识证书，并将其放置在设备上。 当你在 IoT 中心内创建新的设备标识时，将提供两个证书的指纹。 当设备向 IoT 中心进行身份验证时，它会显示其证书，IoT 中心可以验证它们是否与指纹匹配。

  此身份验证方法更加安全，建议用于生产方案。

本文逐步讲解 x.509 证书身份验证的注册和预配过程。 如果要了解如何使用对称密钥设置设备，请参阅 [使用对称密钥身份验证设置 Azure IoT Edge 设备](how-to-manual-provision-symmetric-key.md)。

## <a name="prerequisites"></a>先决条件

在按照本文中的步骤执行操作之前，应该已在设备上安装了 IoT Edge 运行时。 否则，请按照 [安装或卸载 Azure IoT Edge 运行时](how-to-install-iot-edge.md)中的步骤操作。

手动设置 x.509 证书需要 IoT Edge 版本1.0.10 或更高版本。

## <a name="create-certificates-and-thumbprints"></a>创建证书和指纹



<!-- TODO -->

## <a name="register-a-new-device"></a>注册新设备

连接到 IoT 中心的每个设备都有一个设备 ID，用于跟踪云到设备或设备到云的通信。 使用其连接信息配置设备，其中包括 IoT 中心主机名、设备 ID 和设备用于向 IoT 中心进行身份验证的信息。

对于 x.509 证书身份验证，此信息以从设备标识证书获取的 *指纹* 形式提供。 在注册设备时，将为 IoT 中心提供这些指纹，以便服务在连接时能够识别设备。

可以使用多个工具在 IoT 中心注册新的 IoT Edge 设备，并上传其证书指纹。 

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Azure 门户的必备组件

Azure 订阅中的免费或标准 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>在 Azure 门户中创建 IoT Edge 设备

在 Azure 门户的 IoT 中心，IoT Edge 设备的创建和管理独立于不支持 Edge 的 IoT 设备。

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。

1. 在左窗格中，从菜单中选择 " **IoT Edge** "，然后选择 " **添加 IoT Edge 设备**"。

   ![从 Azure 门户添加 IoT Edge 设备](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. 在 " **创建设备** " 页上，提供以下信息：

   * 创建描述性设备 ID。 记下此设备 ID，因为你将在下一部分中使用它。
   * 选择“X.509 自签名”作为身份验证类型。
   * 提供主要和次要标识证书指纹。 对于 sha-1 哈希，指纹值为 40-十六进制字符，对于 SHA-256 哈希值为 64-十六进制字符。

1. 选择“保存”。 

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>在 Azure 门户中查看 IoT Edge 设备

所有连接到 IoT 中心并已启用 Edge 的设备都列在 **IoT Edge** 页上。

![查看 IoT 中心中所有的 IoT Edge 设备](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI 的必备组件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* 环境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.70 或更高版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>通过 Azure CLI 创建 IoT Edge 设备

使用 [az iot hub device-identity create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) 命令在 IoT 中心创建新设备标识。 例如：

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

此命令包含多个参数：

* `--device-id` 或 `-d` ：提供 IoT 中心独有的描述性名称。 记下此设备 ID，因为你将在下一部分中使用它。
* `hub-name` 或 `-n` ：提供 IoT 中心的名称。
* `--edge-enabled` 或 `--ee` ：声明设备是 IoT Edge 设备。
* `--auth-method` 或 `--am` ：声明设备将要使用的授权类型。 在此示例中，我们使用的是 x.509 证书指纹。
* `--primary-thumbprint` 或 `--ptp` ：提供要用作主键的 x.509 证书指纹。
* `--secondary-thumbprint` 或 `--stp` ：提供要用作辅助密钥的 x.509 证书指纹。

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>通过 Azure CLI 查看 IoT Edge 设备

使用 [az iot hub device-identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) 命令在 IoT 中心查看所有设备。 例如：

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

添加标志 `--edge-enabled` 或 `--ee` 仅列出 IoT 中心 IoT Edge 设备。

注册为 IoT Edge 设备的任何设备的 **capabilities.iotEdge** 属性都会设置为 **true**。

--- 

## <a name="configure-an-iot-edge-device"></a>配置 IoT Edge 设备

IoT Edge 设备在 IoT 中心内具有标识后，需要使用其云标识以及标识证书来配置设备。

在 Linux 设备上，可以通过编辑 yaml 文件来提供此信息。 在 Windows 设备上，可以通过运行 PowerShell 脚本来提供此信息。

# <a name="linux"></a>[Linux](#tab/linux)

1. 在 IoT Edge 设备上，打开配置文件。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 查找文件的 "预配配置" 部分。 

1. **使用连接字符串部分注释掉手动设置配置**。

1. **使用 x.509 标识证书部分取消注释手动设置配置**。 请确保 **provisioning:** 行前面没有空格，并且嵌套项缩进了两个空格。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. 更新以下字段：

   * **iothub_hostname**：设备将连接到的 IoT 中心的主机名。 例如，`{IoT hub name}.azure-devices.net`。
   * **device_id**：注册设备时提供的 id。
   * **identity_cert**：设备上的标识证书的 URI。 例如，`file:///path/identity_certificate.pem`。
   * **identity_pk**：提供的标识证书的私钥文件的 URI。 例如，`file:///path/identity_key.pem`。

1. 保存并关闭该文件。

   `CTRL + X`, `Y`, `Enter`

1. 在配置文件中输入预配信息后，重启守护程序：

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。

2. 使用 [IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令可配置计算机上的 IoT Edge 运行时。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * 如果使用的是 Linux 容器，请将 `-ContainerOs` 参数添加到标志。 与你在前面运行的命令所选择的容器选项一致 `Deploy-IoTEdge` 。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * 如果已将 IoTEdgeSecurityDaemon.ps1 脚本下载到设备进行脱机或特定版本安装，请务必引用该脚本的本地副本。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 系统出现提示时，请提供以下信息：

   * **IotHubHostName**：设备将连接到的 IoT 中心的主机名。 例如，`{IoT hub name}.azure-devices.net`。
   * **DeviceId**：注册设备时提供的 ID。
   * **X509IdentityCertificate**：设备上的标识证书的绝对路径。 例如，`C:\path\identity_certificate.pem`。
   * **X509IdentityPrivateKey**：提供的标识证书的私钥文件的绝对路径。 例如，`C:\path\identity_key.pem`。

手动设置设备时，可以使用其他参数来修改该过程，包括：

* 定向流量，使其通过代理服务器
* 声明特定的 edgeAgent 容器映像，并在其位于私有注册表中时提供凭据

有关这些其他参数的详细信息，请参阅 [适用于 Windows 上的 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>后续步骤

继续 [部署 IoT Edge 模块](how-to-deploy-modules-portal.md) ，了解如何将模块部署到设备上。
