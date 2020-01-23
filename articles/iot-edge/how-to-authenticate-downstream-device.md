---
title: 对下游设备进行身份验证-Azure IoT Edge |Microsoft Docs
description: 如何对 IoT 中心的下游设备或叶设备进行身份验证，以及如何通过 Azure IoT Edge 网关设备来路由它们的连接。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1ca066729a81ff51d87c5d8063c94be86366811c
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548758"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>通过 Azure IoT 中心对下游设备进行身份验证

在透明网关方案中，下游设备（有时称为叶设备或子设备）需要 IoT 中心的标识，就像任何其他设备一样。 本文逐步讲解用于向 IoT 中心验证下游设备的选项，并演示如何声明网关连接。

设置成功透明的网关连接有三个常规步骤。 本文介绍第二个步骤：

1. 网关设备需要能够安全地连接到下游设备，接收来自下游设备的通信，并将消息路由到适当的目标。 有关详细信息，请参阅[将 IoT Edge 设备配置为用作透明网关](how-to-create-transparent-gateway.md)。
2. **下游设备需要具有设备标识，才能通过 IoT 中心进行身份验证，并且知道要通过其网关设备进行通信。**
3. 下游设备需要安全连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

下游设备可以使用以下三种方法之一对 IoT 中心进行身份验证：对称密钥（有时称为共享访问密钥）、x.509 自签名证书或 x.509 证书颁发机构（CA）签名证书。 身份验证步骤类似于使用 IoT 中心设置任何非 IoT Edge 设备所使用的步骤，但与声明网关关系的区别略有不同。

本文中的步骤显示了手动设备预配，而不是 Azure IoT 中心设备预配服务（DPS）的自动预配。 不支持通过 DPS 预配下游设备。

## <a name="prerequisites"></a>必备组件

完成[配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)中的步骤。 如果要对下游设备使用 x.509 authentication，则需要使用在透明网关一文中设置的相同证书生成脚本。

本文指向多个点的*网关主机名*。 网关主机名在 IoT Edge 网关设备上的 yaml 文件的**hostname**参数中声明。 它在下游设备的连接字符串中引用。 需要将网关主机名解析为 IP 地址，方法是使用 DNS 或主机文件条目。

## <a name="register-device-symmetric-key"></a>注册设备（对称密钥）

对称密钥身份验证或共享访问密钥身份验证是使用 IoT 中心进行身份验证的最简单方法。 使用对称密钥身份验证时，base64 密钥与 IoT 中心中的 IoT 设备 ID 相关联。 在 IoT 应用程序中包含该密钥，以便设备在连接到 IoT 中心时可以提供该密钥。

### <a name="create-the-device-identity"></a>创建设备标识

使用 Azure 门户、Azure CLI 或 Visual Studio Code 的 IoT 扩展在 IoT 中心中添加新的 IoT 设备。 请记住，下游设备需要在 IoT 中心中标识为常规 IoT 设备，而不是 IoT Edge 设备。

创建新的设备标识时，请提供以下信息：

* 为设备创建 ID。

* 选择 "**对称密钥**" 作为 "身份验证类型"。

* 还可以选择**设置父设备**，并选择此下游设备将连接到的 IoT Edge 网关设备。 对于对称密钥身份验证，此步骤是可选的，但建议这样做，因为设置父设备会启用下游设备的[脱机功能](offline-capabilities.md)。 你随时可以更新设备详细信息，以便以后添加或更改父项。

   ![在门户中创建具有对称密钥身份验证的设备 ID](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

你可以使用[Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的操作。 以下示例创建了具有对称密钥身份验证的新 IoT 设备，并分配了父设备：

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

有关设备创建和父/子管理 Azure CLI 命令的详细信息，请参阅[az iot 中心设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的参考内容。


接下来，[检索和修改连接字符串](#retrieve-and-modify-connection-string)，使你的设备知道通过其网关进行连接。

## <a name="register-device-x509-self-signed"></a>注册设备（x.509 自签名）

对于 x.509 自签名身份验证，有时称为指纹身份验证，则需要创建新的证书以放置在 IoT 设备上。 这些证书在其中有一个指纹，可与 IoT 中心共享以便进行身份验证。

如果没有证书颁发机构来创建 x.509 证书，则可以[创建演示证书来测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。 为下游设备生成测试证书时，请使用为网关设备生成证书的相同根 CA 证书。

1. 使用 CA 证书为下游设备创建两个设备证书（主要证书和辅助证书）。

   设备证书的 "使用者名称" 必须设置为在 Azure IoT 中心注册 IoT 设备时将使用的设备 ID。 此设置是进行身份验证所必需的。

2. 从每个证书中检索 SHA1 指纹（称为 IoT 中心接口中的指纹），这是一个40的十六进制字符串。 使用以下 openssl 命令查看证书并找到指纹：

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   运行此命令两次，一次用于主证书，一次用于辅助证书。 使用自签名 x.509 证书注册新的 IoT 设备时，为这两个证书提供指纹。

3. 在 Azure 门户中导航到 IoT 中心，并使用以下值创建新的 IoT 设备标识：

   * 提供与设备证书的使用者名称匹配的**设备 ID** 。
   * 选择**X.509 自签名**作为身份验证类型。
   * 粘贴从设备的主证书和辅助证书复制的十六进制字符串。
   * 选择 "**设置父设备**"，然后选择此下游设备将连接的 IoT Edge 网关设备。 对于下游设备，需要使用父设备进行 x.509 身份验证。

   ![在门户中通过 x.509 自签名身份验证创建设备 ID](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 将设备证书和密钥复制到下游设备上的任何位置。 同时移动同时生成网关设备证书和下游设备证书的共享根 CA 证书副本。

   你将在连接到 IoT 中心的叶设备应用程序中引用这些文件。 你可以使用诸如[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)之类的服务或类似于[安全复制协议](https://www.ssh.com/ssh/scp/)的函数来移动证书文件。

5. 根据您的首选语言，查看如何在 IoT 应用程序中引用 x.509 证书的示例：

   * C#：[在 Azure IoT 中心设置 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C： [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js： [simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java： [SendEventX509](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python： [send_message_x509 py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

你可以使用[Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例使用 x.509 自签名身份验证创建新的 IoT 设备，并分配父设备：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

有关设备创建、证书生成以及父和子管理 Azure CLI 命令的详细信息，请参阅[az iot 中心设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的参考内容。

接下来，[检索和修改连接字符串](#retrieve-and-modify-connection-string)，使你的设备知道通过其网关进行连接。

## <a name="register-device-x509-ca-signed"></a>注册设备（x.509 CA 已签名）

对于 x.509 证书颁发机构（CA）签名的身份验证，需要一个在 IoT 中心注册的根 CA 证书，用于为 IoT 设备签署证书。 将允许使用由根 CA 证书或其任何中间证书颁发的证书颁发的任何设备进行身份验证。

本部分基于 IoT 中心文章在[Azure IoT 中心设置 x.509 安全性中](../iot-hub/iot-hub-security-x509-get-started.md)详述的说明。 按照本部分中的步骤来了解要使用哪些值设置通过网关连接的下游设备。

如果没有证书颁发机构来创建 x.509 证书，则可以[创建演示证书来测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。 为下游设备生成测试证书时，请使用为网关设备生成证书的相同根 CA 证书。

1. 按照在*Azure iot 中心设置 x.509 安全性中的将* [X.509 CA 证书注册到 IoT 中心](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)部分中的说明进行操作。 在该部分中，执行以下步骤：

   1. 上传根 CA 证书。 如果使用的是演示证书，根 CA **\<路径 >/certs/azure-iot-test-only.root.ca.cert.pem**。

   2. 验证你拥有此根 CA 证书。

2. 按照在*Azure iot 中心设置 x.509 安全性*部分的为[IoT 中心创建 x.509 设备](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)部分中的说明进行操作。 在该部分中，执行以下步骤：

   1. 添加新设备。 提供 "**设备 ID**" 的小写名称，然后选择 "身份验证类型**x.509 CA**"。
   2. 设置父设备。 对于下游设备，请选择 "**设置父设备**"，然后选择将提供与 IoT 中心的连接的 IoT Edge 网关设备。

3. 为下游设备创建证书链。 使用上载到 IoT 中心的相同根 CA 证书建立此链。 使用为门户中的设备标识提供的相同小写设备 ID。

4. 将设备证书和密钥复制到下游设备上的任何位置。 同时移动同时生成网关设备证书和下游设备证书的共享根 CA 证书副本。

   你将在连接到 IoT 中心的叶设备应用程序中引用这些文件。 你可以使用诸如[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)之类的服务或类似于[安全复制协议](https://www.ssh.com/ssh/scp/)的函数来移动证书文件。

5. 根据您的首选语言，查看如何在 IoT 应用程序中引用 x.509 证书的示例：

   * C#：[在 Azure IoT 中心设置 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C： [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js： [simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java： [SendEventX509](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)
   * Python： [send_message_x509 py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/advanced-hub-scenarios/send_message_x509.py)

你可以使用[Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例使用 x.509 CA 签名身份验证创建新的 IoT 设备，并分配父设备：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

有关详细信息，请参阅[az iot 中心设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的 Azure CLI 参考内容。

接下来，[检索和修改连接字符串](#retrieve-and-modify-connection-string)，使你的设备知道通过其网关进行连接。

## <a name="retrieve-and-modify-connection-string"></a>检索和修改连接字符串

在门户中创建 IoT 设备标识后，可以检索其主密钥或辅助密钥。 其中一个密钥需要包含在应用程序用来与 IoT 中心通信的连接字符串中。 对于对称密钥身份验证，IoT 中心会在设备详细信息中提供完全形成的连接字符串，以方便您方便。 需要将有关网关设备的额外信息添加到连接字符串。

下游设备的连接字符串需要以下组件：

* 设备连接到的 IoT 中心： `Hostname={iothub name}.azure-devices.net`
* 向中心注册的设备 ID： `DeviceID={device ID}`
* 主密钥或辅助密钥： `SharedAccessKey={key}`
* 设备通过连接的网关设备。 提供 IoT Edge 网关设备的 yaml 文件中的**主机名**值： `GatewayHostName={gateway hostname}`

总之，完整的连接字符串如下所示：

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果已为此下游设备建立父/子关系，则可以通过直接调用网关作为连接主机来简化连接字符串。 父/子关系是 x.509 身份验证所必需的，但对于对称密钥身份验证是可选的。 例如：

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

此时，应已注册 IoT Edge 设备并将其配置为网关。 还会注册下游 IoT 设备，并将其指向其网关设备。 最后一步是将证书放置在下游设备上，以便可以安全地连接到网关。

继续阅读网关系列的下一篇文章，[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

## <a name="next-steps"></a>后续步骤

完成本文后，应该有一个 IoT Edge 设备作为透明网关，并向 IoT 中心注册了下游设备。 接下来，需要将下游设备配置为信任网关设备，并安全地连接到该设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。
