---
title: 对下游设备进行身份验证 - Azure IoT Edge | Microsoft Docs
description: 了解如何在 IoT 中心对下游设备（叶设备）进行身份验证，并通过 Azure IoT Edge 网关设备路由其连接。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7a032056a684107de3dd00fe4861f34c013a80db
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003617"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>通过 Azure IoT 中心对下游设备进行身份验证

在透明网关方案中，与任何其他设备一样，下游设备（有时称为叶设备或子设备）需要在 IoT 中心内拥有标识。 本文将会逐步介绍用于在 IoT 中心对下游设备进行身份验证的选项，然后演示如何声明网关连接。

成功设置透明网关连接需要完成三个常规步骤。 本文将介绍其中的第二个步骤：

1. 网关设备需要能够安全连接到下游设备，从下游设备接收通信，并将消息路由到正确的目标。 有关详细信息，请参阅[将 IoT Edge 设备配置为充当透明网关](how-to-create-transparent-gateway.md)。
2. **下游设备需有一个设备标识，才能在 IoT 中心进行身份验证并知道要通过其网关设备进行通信。**
3. 下游设备需要能够安全连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

下游设备可以使用以下三种方法之一在 IoT 中心进行身份验证：对称密钥（有时称为共享访问密钥）、X.509 自签名证书，或 X.509 证书颁发机构 (CA) 签名的证书。 身份验证步骤用于在 IoT 中心设置任何非 IoT Edge 设备的步骤类似，只是在声明网关关系方面有细微的差别。

本文中的步骤与手动设备预配相关，而与使用 Azure IoT 中心设备预配服务进行的自动预配无关。 

## <a name="prerequisites"></a>先决条件

完成[配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)中的步骤。

本文在多个位置提到了“网关主机名”。 网关主机名在 IoT Edge 网关设备上的 config.yaml 文件的 **hostname** 参数中声明。 它用于创建本文中所用的证书，并在下游设备的连接字符串中引用。 网关主机名必须能够解析成 IP 地址，不管是使用 DNS 还是主机文件条目。

## <a name="symmetric-key-authentication"></a>对称密钥身份验证

对称密钥身份验证（也称为共享访问密钥身份验证）是在 IoT 中心进行身份验证的最简单方法。 使用对称密钥身份验证时，某个 base64 密钥将与 IoT 中心内的 IoT 设备 ID 相关联。 需将该密钥包含在 IoT 应用程序中，使设备在连接到 IoT 中心时可以出示该密钥。 

### <a name="create-the-device-identity"></a>创建设备标识 

使用 Azure 门户、Azure CLI 或适用于 Visual Studio Code 的 IoT 扩展在 IoT 中心添加新的 IoT 设备。 请记住，需要在 IoT 中心将下游设备标识为常规 IoT 设备，而不是 IoT Edge 设备。 

创建新的设备标识时，请提供以下信息： 

* 创建设备的 ID。

* 选择“对称密钥”作为身份验证类型。 

* （可选）选择“设置父设备”，并选择下游设备用来建立连接的 IoT Edge 网关设备。 对于对称密钥身份验证，此步骤是可选的，但建议执行此步骤，因为设置父设备可为下游设备启用[脱机功能](offline-capabilities.md)。 以后始终可以更新设备详细信息来添加或更改父设备。 

   ![在门户中使用对称密钥身份验证创建设备 ID](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

可以使用[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的操作。 以下示例使用对称密钥身份验证创建新的 IoT 设备，并分配父设备： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

有关用于创建设备和管理父/子设备的 Azure CLI 命令的详细信息，请参阅 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 命令的参考内容。

### <a name="connect-to-iot-hub-through-a-gateway"></a>通过网关连接到 IoT 中心

使用对称密钥在 IoT 中心对常规 IoT 设备进行身份验证的过程同样适用于下游设备。 唯一的差别在于，需要添加指向网关设备的指针，以路由连接或者在离线场景中代表 IoT 中心处理身份验证。 

对于对称密钥身份验证，无需在设备上执行任何附加步骤，即可在 IoT 中心对其进行身份验证。 仍需准备好证书，使下游设备可以连接到其网关设备。具体请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

在门户中创建 IoT 设备标识后，可以检索其主要密钥或辅助密钥。 需要将其中的一个密钥包含在要与 IoT 中心通信的任何应用程序中的连接字符串内。 对于对称密钥身份验证，出于方便，IoT 中心将在设备详细信息中提供完整格式的连接字符串。 你需要将有关网关设备的附加信息添加到该连接字符串。 

下游设备的对称密钥连接字符串需要包含以下组成部分： 

* 设备连接到的 IoT 中心：`Hostname={iothub name}.azure-devices.net`
* 已注册到中心的设备 ID：`DeviceID={device ID}`
* 主要密钥或辅助密钥：`SharedAccessKey={key}`
* 设备用来建立连接的网关设备。 请提供 IoT Edge 网关设备 config.yaml 文件中的 **hostname** 值：`GatewayHostName={gateway hostname}`

所有这些组成部分共同构成了如下所示的完整连接字符串：

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果你为此下游设备建立了父/子关系，则可以通过直接调用充当连接主机的网关来简化连接字符串。 例如： 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509 身份验证 

可通过两种方式使用 X.509 证书对 IoT 设备进行身份验证。 无论选择哪种身份验证方式，将设备连接到 IoT 中心的步骤都是相同的。 选择自签名证书或 CA 签名的证书用于身份验证，然后继续了解如何连接到 IoT 中心。 

有关 IoT 中心如何使用 X.509 身份验证的详细信息，请参阅以下文章： 
* [使用 X.509 CA 证书进行设备身份验证](../iot-hub/iot-hub-x509ca-overview.md)
* [IoT 行业中 X.509 CA 证书的概念](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>使用 X.509 自签名证书创建设备标识

对于 X.509 自签名身份验证（有时称为指纹身份验证），需要创建新证书并将其放在 IoT 设备上。 这些证书包含一个指纹，你可以与 IoT 中心共享该指纹以完成身份验证。 

测试此方案的最简单方法是使用在[将 IoT Edge 设备配置为充当透明网关](how-to-create-transparent-gateway.md)一文中创建证书时所用的同一台计算机。 该计算机中应已设置适当的工具、根 CA 证书和中间 CA 证书用于创建 IoT 设备证书。 然后，可将最终的证书及其私钥复制到下游设备。 在有关网关的文章中，你已遵循相应的步骤在计算机上设置了 openssl，并克隆了 IoT Edge 存储库用于访问证书创建脚本。 然后，创建了一个名为 **\<WRKDIR>** 的工作目录用于保存证书。 默认证书用于开发和测试，因此有效期仅为 30 天。 你应已创建一个根 CA 证书和一个中间证书。 

1. 在 bash 或 PowerShell 窗口中导航到该工作目录。 

2. 为下游设备创建两个证书（主要和辅助）。 提供设备名称，然后指定主要或辅助标签。 此信息用于将文件命名，使你能够跟踪多个设备的证书。 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. 检索每个证书的 SHA1 指纹 - 包含 40 个十六进制字符的字符串。 使用以下 openssl 命令查看证书并查找指纹：

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. 在 Azure 门户中导航到你的 IoT 中心，并使用以下值创建新的 IoT 设备标识： 

   * 选择“X.509 自签名”作为身份验证类型。
   * 粘贴从设备的主要和辅助证书中复制的十六进制字符串。
   * 选择“设置父设备”，并选择下游设备用来建立连接的 IoT Edge 网关设备。 对下游设备进行 X.509 身份验证时，需要使用父设备。 

   ![在门户中使用 X.509 自签名身份验证创建设备 ID](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. 将以下文件复制到下游设备上的任一目录：

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   你将在连接到 IoT 中心的叶设备应用程序中引用这些文件。 可以使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 之类的服务或[安全复制协议](https://www.ssh.com/ssh/scp/)之类的功能来移动证书文件。

可以使用[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例使用 X.509 自签名身份验证创建新的 IoT 设备，并分配父设备： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

有关用于创建设备、生成证书和管理父/子设备的 Azure CLI 命令的详细信息，请参阅 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 命令的参考内容。

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>使用 X.509 CA 签名的证书创建设备标识

对于 X.509 证书颁发机构 (CA) 签名的身份验证，需要在 IoT 中心注册一个用来为 IoT 设备证书签名的根 CA 证书。 使用根 CA 证书或其任何中间证书颁发的证书的任何设备都可进行身份验证。 

本部分基于 IoT 中心文章[在 Azure IoT 中心设置 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)中详述的说明。 请遵循本部分所述的步骤了解要使用哪些值来设置通过网关进行连接的下游设备。 

测试此方案的最简单方法是使用在[将 IoT Edge 设备配置为充当透明网关](how-to-create-transparent-gateway.md)一文中创建证书时所用的同一台计算机。 该计算机中应已设置适当的工具、根 CA 证书和中间 CA 证书用于创建 IoT 设备证书。 然后，可将最终的证书及其私钥复制到下游设备。 在有关网关的文章中，你已遵循相应的步骤在计算机上设置了 openssl，并克隆了 IoT Edge 存储库用于访问证书创建脚本。 然后，创建了一个名为 **\<WRKDIR>** 的工作目录用于保存证书。 默认证书用于开发和测试，因此有效期仅为 30 天。 你应已创建一个根 CA 证书和一个中间证书。 

1. 请遵照“在 Azure IoT 中心设置 X.509 安全性”的[将 X.509 CA 证书注册到 IoT 中心](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)部分中的说明操作。 在本部分，你将执行以下步骤： 

   1. 上传根 CA 证书。 如果使用的证书是在有关透明网关的文章中创建的，请上传 **\<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem** 作为根证书文件。 
   2. 验证你是否拥有该根 CA 证书。 可以使用 \<WRKDIR> 中的证书工具验证拥有权。 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. 请遵照“在 Azure IoT 中心设置 X.509 安全性”的[为 IoT 中心创建 X.509 设备](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)部分中的说明操作。 在本部分，你将执行以下步骤： 

   1. 添加新设备。 为**设备 ID** 提供小写名称，并选择身份验证类型“X.509 CA 签名”。 
   2. 设置父设备。 对于下游设备，请选择“设置父设备”，并选择用来与 IoT 中心建立连接的 IoT Edge 网关设备。 

3. 创建下游设备的证书链。 使用上传到 IoT 中心的同一根 CA 证书来建立此链。 使用在门户中提供给设备标识的相同小写设备 ID。

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. 将以下文件复制到下游设备上的任一目录： 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   你将在连接到 IoT 中心的叶设备应用程序中引用这些文件。 可以使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 之类的服务或[安全复制协议](https://www.ssh.com/ssh/scp/)之类的功能来移动证书文件。

可以使用[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例使用 X.509 CA 签名身份验证创建新的 IoT 设备，并分配父设备： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

有关用于创建设备和管理父/子设备的 Azure CLI 命令的详细信息，请参阅 [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 命令的参考内容。


### <a name="connect-to-iot-hub-through-a-gateway"></a>通过网关连接到 IoT 中心

每个 Azure IoT SDK 处理 X.509 身份验证的方式略有不同。 但是，使用 X.509 证书在 IoT 中心对常规 IoT 设备进行身份验证的过程同样适用于下游设备。 唯一的差别在于，需要添加指向网关设备的指针，以路由连接或者在离线场景中代表 IoT 中心处理身份验证。 一般情况下，可对所有 IoT 中心设备遵循相同的 X.509 身份验证步骤，只需将连接字符串中的 **Hostname** 值替换为网关设备的主机名即可。 

以下部分演示了不同 SDK 语言的一些示例。 

>[!IMPORTANT]
>以下示例演示 IoT 中心 SDK 如何使用证书对设备进行身份验证。 在生产部署中，应在硬件安全模块 (HSM) 中存储私钥或 SAS 密钥等所有机密。 

#### <a name="net"></a>.NET

有关使用 X.509 证书在 IoT 中心进行身份验证的 C# 程序示例，请参阅[在 Azure IoT 中心设置 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)。 此处包含了该示例的某些关键代码行，以演示身份验证过程。

在声明 DeviceClient 实例的主机名时，请使用 IoT Edge 网关设备的主机名。 可在网关设备的 config.yaml 文件中找到该主机名。 

如果使用 IoT Edge Git 存储库提供的测试证书，该证书的密钥为 **1234**。

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

有关使用 X.509 证书在 IoT 中心进行身份验证的 C 程序示例，请参阅 C IoT SDK 的 [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) 示例。 此处包含了该示例的某些关键代码行，以演示身份验证过程。

定义下游设备的连接字符串时，请对 **HostName** 参数使用 IoT Edge 网关设备的主机名。 可在网关设备的 config.yaml 文件中找到该主机名。 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

有关使用 X.509 证书在 IoT 中心进行身份验证的 Node.js 程序示例，请参阅 Node.js IoT SDK 的 [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) 示例。 此处包含了该示例的某些关键代码行，以演示身份验证过程。

定义下游设备的连接字符串时，请对 **HostName** 参数使用 IoT Edge 网关设备的主机名。 可在网关设备的 config.yaml 文件中找到该主机名。 

如果使用 IoT Edge Git 存储库提供的测试证书，该证书的密钥为 **1234**。

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Python SDK 目前仅支持使用 X509 证书和文件中的密钥，而不支持以内联方式定义的证书和密钥。 在下面的示例中，相关 filepaths 存储在环境变量中。

定义下游设备的主机名时，请使用 IoT Edge 的网关设备主机名作为**hostname**参数。 可在网关设备的 config.yaml 文件中找到该主机名。 

```python
import os
from azure.iot.device import IoTHubDeviceClient, X509

HOSTNAME = "[IoT Edge Gateway Hostname]"
DEVICE_ID = "[Device ID]"

def iothub_client_init():
    x509 = X509(
        cert_file=os.getenv("X509_CERT_FILE"),
        key_file=os.getenv("X509_KEY_FILE")
    )

    client = IoTHubDeviceClient.create_from_x509_certificate(
        x509=x509,
        hostname=HOSTNAME,
        device_id=DEVICE_ID
    )
)

if __name__ == '__main__':
    iothub_client_init()
```

#### <a name="java"></a>Java

有关使用 X.509 证书在 IoT 中心进行身份验证的 Java 程序示例，请参阅 Java IoT SDK 的 [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) 示例。 此处包含了该示例的某些关键代码行，以演示身份验证过程。

定义下游设备的连接字符串时，请对 **HostName** 参数使用 IoT Edge 网关设备的主机名。 可在网关设备的 config.yaml 文件中找到该主机名。 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>后续步骤

完成本文后，你应已获得一个充当透明网关的 IoT Edge 设备，以及一个已注册到 IoT 中心的下游设备。 接下来，需要配置下游设备，以信任网关设备并向其发送消息。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。
