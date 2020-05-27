---
title: 对下游设备进行身份验证 - Azure IoT Edge | Microsoft Docs
description: 了解如何在 IoT 中心对下游设备（叶设备）进行身份验证，并通过 Azure IoT Edge 网关设备路由其连接。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3edd29703f74c7671537fbcf08159dd830e5453c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726220"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>通过 Azure IoT 中心对下游设备进行身份验证

在透明网关方案中，与任何其他设备一样，下游设备（有时称为叶设备或子设备）需要在 IoT 中心内拥有标识。 本文将会逐步介绍用于在 IoT 中心对下游设备进行身份验证的选项，然后演示如何声明网关连接。

成功设置透明网关连接需要完成三个常规步骤。 本文将介绍其中的第二个步骤：

1. 网关设备需要能够安全连接到下游设备，从下游设备接收通信，并将消息路由到正确的目标。 有关详细信息，请参阅[将 IoT Edge 设备配置为充当透明网关](how-to-create-transparent-gateway.md)。
2. **下游设备需有一个设备标识，才能在 IoT 中心进行身份验证并知道要通过其网关设备进行通信。**
3. 下游设备需安全连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

下游设备可以使用以下三种方法之一在 IoT 中心进行身份验证：对称密钥（有时称为共享访问密钥）、X.509 自签名证书，或 X.509 证书颁发机构 (CA) 签名的证书。 身份验证步骤用于在 IoT 中心设置任何非 IoT Edge 设备的步骤类似，只是在声明网关关系方面有细微的差别。

本文中的步骤与手动设备预配相关，而与使用 Azure IoT 中心设备预配服务 (DPS) 进行的自动预配无关。 不支持通过 DPS 预配下游设备。

## <a name="prerequisites"></a>先决条件

完成[配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)中的步骤。 如果要对下游设备使用 X.509 身份验证，则需要使用在透明网关文章中设置的相同的证书生成脚本。

本文在多个位置提到了“网关主机名”  。 网关主机名在 IoT Edge 网关设备上的 config.yaml 文件的 **hostname** 参数中声明。 下游设备的连接字符串中引用了它。 网关主机名必须能够解析成 IP 地址，不管是使用 DNS 还是主机文件条目。

## <a name="register-device-symmetric-key"></a>注册设备（对称密钥）

对称密钥身份验证（也称为共享访问密钥身份验证）是在 IoT 中心进行身份验证的最简单方法。 使用对称密钥身份验证时，某个 base64 密钥将与 IoT 中心内的 IoT 设备 ID 相关联。 需将该密钥包含在 IoT 应用程序中，使设备在连接到 IoT 中心时可以出示该密钥。

### <a name="create-the-device-identity"></a>创建设备标识

使用 Azure 门户、Azure CLI 或适用于 Visual Studio Code 的 IoT 扩展在 IoT 中心添加新的 IoT 设备。 请记住，需要在 IoT 中心将下游设备标识为常规 IoT 设备，而不是 IoT Edge 设备。

创建新的设备标识时，请提供以下信息：

* 创建设备的 ID。

* 选择“对称密钥”作为身份验证类型。 

* （可选）选择“设置父设备”，并选择下游设备用来建立连接的 IoT Edge 网关设备。  对于对称密钥身份验证，此步骤是可选的，但建议执行此步骤，因为设置父设备可为下游设备启用[脱机功能](offline-capabilities.md)。 以后始终可以更新设备详细信息来添加或更改父设备。

   ![在门户中使用对称密钥身份验证创建设备 ID](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

可以使用[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的操作。 以下示例使用对称密钥身份验证创建新的 IoT 设备，并分配父设备：

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

有关用于创建设备和管理父/子设备的 Azure CLI 命令的详细信息，请参阅 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) 命令的参考内容。


接下来，[检索并修改连接字符串](#retrieve-and-modify-connection-string)，从而使设备知道通过其网关进行连接。

## <a name="register-device-x509-self-signed"></a>注册设备（X.509 自签名）

对于 X.509 自签名身份验证（有时称为指纹身份验证），需要创建新证书并将其放在 IoT 设备上。 这些证书包含一个指纹，你可以与 IoT 中心共享该指纹以完成身份验证。

如果没有用于创建 X.509 证书的证书颁发机构，可[创建演示证书以测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。 为下游设备生成测试证书时，使用为网关设备生成证书所用的根 CA 证书。

1. 使用 CA 证书，为下游设备创建两个设备证书（主要和辅助）。

   设备证书必须将“使用者名称”设置为在 Azure IoT 中心注册 IoT 设备时将使用的设备 ID。 身份验证需要此设置。

2. 检索每个证书的 SHA1 指纹 - 包含 40 个十六进制字符的字符串。 使用以下 openssl 命令查看证书并查找指纹：

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   运行此命令两次，一次针对主要证书，另一次针对辅助证书。 使用自签名的 X.509 证书注册新 IoT 设备时，为这两个证书提供指纹。

3. 在 Azure 门户中导航到你的 IoT 中心，并使用以下值创建新的 IoT 设备标识：

   * 提供与设备证书的使用者名称匹配的设备 ID  。
   * 选择“X.509 自签名”作为身份验证类型。 
   * 粘贴从设备的主要和辅助证书中复制的十六进制字符串。
   * 选择“设置父设备”，并选择下游设备用来建立连接的 IoT Edge 网关设备。  对下游设备进行 X.509 身份验证时，需要使用父设备。

   ![在门户中使用 X.509 自签名身份验证创建设备 ID](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. 将设备证书和密钥复制到下游设备上的任何位置。 此外，移动生成网关设备证书和下游设备证书的共享根 CA 证书的副本。

   你将在连接到 IoT 中心的叶设备应用程序中引用这些文件。 可以使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 之类的服务或[安全复制协议](https://www.ssh.com/ssh/scp/)之类的功能来移动证书文件。

5. 根据首选语言，查看在 IoT 应用程序中引用 X.509 证书的示例：

   * C#：[在 Azure IoT 中心设置 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js：[simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java:[SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python：[send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

可以使用[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例使用 X.509 自签名身份验证创建新的 IoT 设备，并分配父设备：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

有关用于创建设备、生成证书和管理父/子设备的 Azure CLI 命令的详细信息，请参阅 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) 命令的参考内容。

接下来，[检索并修改连接字符串](#retrieve-and-modify-connection-string)，从而使设备知道通过其网关进行连接。

## <a name="register-device-x509-ca-signed"></a>注册设备（X.509 CA 签名）

对于 X.509 证书颁发机构 (CA) 签名的身份验证，需要在 IoT 中心注册一个用来为 IoT 设备证书签名的根 CA 证书。 使用根 CA 证书或其任何中间证书颁发的证书的任何设备都可进行身份验证。

本部分基于 IoT 中心文章[在 Azure IoT 中心设置 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)中详述的说明。 请遵循本部分所述的步骤了解要使用哪些值来设置通过网关进行连接的下游设备。

如果没有用于创建 X.509 证书的证书颁发机构，可[创建演示证书以测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。 为下游设备生成测试证书时，使用为网关设备生成证书所用的根 CA 证书。

1. 请遵照“在 Azure IoT 中心设置 X.509 安全性”的[将 X.509 CA 证书注册到 IoT 中心](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)部分中的说明操作。  在本部分，你将执行以下步骤：

   1. 上传根 CA 证书。 如果使用演示证书，则根 CA 为 \<path>/certs/azure-iot-test-only.root.ca.cert.pem  。

   2. 验证你是否拥有该根 CA 证书。

2. 请遵照“在 Azure IoT 中心设置 X.509 安全性”的[为 IoT 中心创建 X.509 设备](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)部分中的说明操作。  在本部分，你将执行以下步骤：

   1. 添加新设备。 为**设备 ID** 提供小写名称，并选择身份验证类型“X.509 CA 签名”。 
   2. 设置父设备。 对于下游设备，请选择“设置父设备”，并选择用来与 IoT 中心建立连接的 IoT Edge 网关设备。 

3. 创建下游设备的证书链。 使用上传到 IoT 中心的同一根 CA 证书来建立此链。 使用在门户中提供给设备标识的相同小写设备 ID。

4. 将设备证书和密钥复制到下游设备上的任何位置。 此外，移动生成网关设备证书和下游设备证书的共享根 CA 证书的副本。

   你将在连接到 IoT 中心的叶设备应用程序中引用这些文件。 可以使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 之类的服务或[安全复制协议](https://www.ssh.com/ssh/scp/)之类的功能来移动证书文件。

5. 根据首选语言，查看在 IoT 应用程序中引用 X.509 证书的示例：

   * C#：[在 Azure IoT 中心设置 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js：[simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java:[SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python：[send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

可以使用[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例使用 X.509 CA 签名身份验证创建新的 IoT 设备，并分配父设备：

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

有关详细信息，请参阅 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest) 命令的 Azure CLI 参考内容。

接下来，[检索并修改连接字符串](#retrieve-and-modify-connection-string)，从而使设备知道通过其网关进行连接。

## <a name="retrieve-and-modify-connection-string"></a>检索并修改连接字符串

在门户中创建 IoT 设备标识后，可以检索其主要密钥或辅助密钥。 这些密钥中的一个需要包含在应用程序用于与 IoT 中心通信的连接字符串中。 对于对称密钥身份验证，出于方便，IoT 中心将在设备详细信息中提供完整格式的连接字符串。 你需要将有关网关设备的附加信息添加到该连接字符串。

下游设备的连接字符串需要包含以下组成部分：

* 设备连接到的 IoT 中心：`Hostname={iothub name}.azure-devices.net`
* 已注册到中心的设备 ID：`DeviceID={device ID}`
* 主要密钥或辅助密钥：`SharedAccessKey={key}`
* 设备用来建立连接的网关设备。 请提供 IoT Edge 网关设备 config.yaml 文件中的 **hostname** 值：`GatewayHostName={gateway hostname}`

所有这些组成部分共同构成了如下所示的完整连接字符串：

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果你为此下游设备建立了父/子关系，则可以通过直接调用充当连接主机的网关来简化连接字符串。 父/子关系对于 X.509 身份验证是必需的，但对于对称密钥身份验证是可选的。 例如：

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

此时，你应已注册了一个 IoT Edge 设备并已将其配置为网关。 还注册了一个下游 IoT 设备，并将其指向其网关设备。 最后一步是将证书置于下游设备上，使其可安全地连接到网关。

继续阅读网关系列的下一篇文章：[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

## <a name="next-steps"></a>后续步骤

完成本文后，你应已获得一个充当透明网关的 IoT Edge 设备，以及一个已注册到 IoT 中心的下游设备。 接下来，需要配置下游设备，以信任网关设备并安全地连接到该设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。
