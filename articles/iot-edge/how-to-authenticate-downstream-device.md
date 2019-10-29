---
title: 对下游设备进行身份验证-Azure IoT Edge |Microsoft Docs
description: 如何对 IoT 中心的下游设备或叶设备进行身份验证，以及如何通过 Azure IoT Edge 网关设备来路由它们的连接。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 09/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1e184691ebbd34de0f69e93419d9c34ab18edbe6
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025936"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>通过 Azure IoT 中心对下游设备进行身份验证

在透明网关方案中，下游设备（有时称为叶设备或子设备）需要 IoT 中心的标识，就像任何其他设备一样。 本文逐步讲解用于向 IoT 中心验证下游设备的选项，并演示如何声明网关连接。

设置成功透明的网关连接有三个常规步骤。 本文介绍第二个步骤：

1. 网关设备需要能够安全地连接到下游设备，接收来自下游设备的通信，并将消息路由到适当的目标。 有关详细信息，请参阅[将 IoT Edge 设备配置为用作透明网关](how-to-create-transparent-gateway.md)。
2. **下游设备需要具有设备标识，才能通过 IoT 中心进行身份验证，并且知道要通过其网关设备进行通信。**
3. 下游设备需要能够安全地连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

下游设备可以使用以下三种方法之一对 IoT 中心进行身份验证：对称密钥（有时称为共享访问密钥）、x.509 自签名证书或 x.509 证书颁发机构（CA）签名证书。 身份验证步骤类似于使用 IoT 中心设置任何非 IoT Edge 设备所使用的步骤，但与声明网关关系的区别略有不同。

本文中的步骤显示了手动设备预配，而不是 Azure IoT 中心设备预配服务的自动预配。 

## <a name="prerequisites"></a>必备组件

完成[配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)中的步骤。 如果要对下游设备使用 x.509 authentication，则需要使用在透明网关一文中设置的相同证书生成脚本。 

本文指向多个点的*网关主机名*。 网关主机名在 IoT Edge 网关设备上的 yaml 文件的**hostname**参数中声明。 它用于在本文中创建证书，并在下游设备的连接字符串中引用。 需要将网关主机名解析为 IP 地址，方法是使用 DNS 或主机文件条目。

## <a name="symmetric-key-authentication"></a>对称密钥身份验证

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
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

有关设备创建和父/子管理 Azure CLI 命令的详细信息，请参阅[az iot 中心设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的参考内容。

### <a name="connect-to-iot-hub-through-a-gateway"></a>通过网关连接到 IoT 中心

使用对称密钥对 IoT 中心的常规 IoT 设备进行身份验证的过程同样适用于下游设备。 唯一的区别在于，你需要添加指向网关设备的指针来路由连接，或在脱机方案中添加以代表 IoT 中心处理身份验证。 

对于对称密钥身份验证，不需要在设备上执行任何其他步骤来对 IoT 中心进行身份验证。 你仍需要准备好证书，以便下游设备能够连接到其网关设备，如[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)中所述。

在门户中创建 IoT 设备标识后，可以检索其主密钥或辅助密钥。 其中一个密钥需要包含在与 IoT 中心通信的任何应用程序中包含的连接字符串中。 对于对称密钥身份验证，IoT 中心会在设备详细信息中提供完全形成的连接字符串，以方便您方便。 需要将有关网关设备的额外信息添加到连接字符串。 

下游设备的对称密钥连接字符串需要以下组件： 

* 设备连接到的 IoT 中心： `Hostname={iothub name}.azure-devices.net`
* 向中心注册的设备 ID： `DeviceID={device ID}`
* 主密钥或辅助密钥： `SharedAccessKey={key}`
* 设备通过连接的网关设备。 提供 IoT Edge 网关设备的 yaml 文件中的**主机名**值： `GatewayHostName={gateway hostname}`

总之，完整的连接字符串如下所示：

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果已为此下游设备建立父/子关系，则可以通过直接调用网关作为连接主机来简化连接字符串。 例如： 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509 authentication 

可以通过两种方式使用 x.509 证书对 IoT 设备进行身份验证。 无论选择进行身份验证的方式如何，将设备连接到 IoT 中心的步骤都是相同的。 选择用于身份验证的自签名证书或 CA 签名证书，然后继续了解如何连接到 IoT 中心。 

有关 IoT 中心如何使用 x.509 authentication 的详细信息，请参阅以下文章： 
* [使用 x.509 CA 证书进行设备身份验证](../iot-hub/iot-hub-x509ca-overview.md)
* [概念上了解 IoT 行业中的 x.509 CA 证书](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>用 x.509 自签名证书创建设备标识

对于 x.509 自签名身份验证，有时称为指纹身份验证，则需要创建新的证书以放置在 IoT 设备上。 这些证书在其中有一个指纹，可与 IoT 中心共享以便进行身份验证。 

若要测试此方案，最简单的方法是使用用于在中创建证书的同一台计算机[配置 IoT Edge 设备，使其充当透明网关](how-to-create-transparent-gateway.md)。 应该已设置该计算机，可以通过正确的工具、根 CA 证书和中间 CA 证书来创建 IoT 设备证书。 你可以随后将最终证书及其私钥复制到下游设备。 按照网关一文中的步骤操作，在计算机上设置 openssl，然后克隆 IoT Edge 存储库以访问证书创建脚本。 然后，你创建了一个工作目录， **\<WRKDIR >** 来保存证书。 默认证书适用于开发和测试，因此仅限过去30天。 应该已创建根 CA 证书和中间证书。 

1. 在 bash 或 PowerShell 窗口中导航到工作目录。 

2. 为下游设备创建两个证书（主证书和辅助证书）。 提供设备名称，然后输入主标签或辅助标签。 此信息用于命名文件，以便你可以跟踪多个设备的证书。 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. 从每个证书中检索 SHA1 指纹（称为 IoT 中心接口中的指纹），这是一个40的十六进制字符串。 使用以下 openssl 命令查看证书并找到指纹：

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. 在 Azure 门户中导航到 IoT 中心，并使用以下值创建新的 IoT 设备标识： 

   * 选择**X.509 自签名**作为身份验证类型。
   * 粘贴从设备的主证书和辅助证书复制的十六进制字符串。
   * 选择 "**设置父设备**"，然后选择此下游设备将连接的 IoT Edge 网关设备。 对于下游设备，需要使用父设备进行 x.509 身份验证。 

   ![在门户中通过 x.509 自签名身份验证创建设备 ID](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. 将以下文件复制到下游设备上的任何目录中：

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   你将在连接到 IoT 中心的叶设备应用程序中引用这些文件。 你可以使用诸如[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)之类的服务或类似于[安全复制协议](https://www.ssh.com/ssh/scp/)的函数来移动证书文件。

你可以使用[Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例使用 x.509 自签名身份验证创建新的 IoT 设备，并分配父设备： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

有关设备创建、证书生成以及父和子管理 Azure CLI 命令的详细信息，请参阅[az iot 中心设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的参考内容。

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>用 x.509 CA 签名证书创建设备标识

对于 x.509 证书颁发机构（CA）签名的身份验证，需要一个在 IoT 中心注册的根 CA 证书，用于为 IoT 设备签署证书。 将允许使用由根 CA 证书或其任何中间证书颁发的证书颁发的任何设备进行身份验证。 

本部分基于 IoT 中心文章在[Azure IoT 中心设置 x.509 安全性中](../iot-hub/iot-hub-security-x509-get-started.md)详述的说明。 按照本部分中的步骤来了解要使用哪些值设置通过网关连接的下游设备。 

若要测试此方案，最简单的方法是使用用于在中创建证书的同一台计算机[配置 IoT Edge 设备，使其充当透明网关](how-to-create-transparent-gateway.md)。 应该已设置该计算机，可以通过正确的工具、根 CA 证书和中间 CA 证书来创建 IoT 设备证书。 你可以随后将最终证书及其私钥复制到下游设备。 按照网关一文中的步骤操作，在计算机上设置 openssl，然后克隆 IoT Edge 存储库以访问证书创建脚本。 然后，你创建了一个工作目录， **\<WRKDIR >** 来保存证书。 默认证书适用于开发和测试，因此仅限过去30天。 应该已创建根 CA 证书和中间证书。 

1. 按照在*Azure iot 中心设置 x.509 安全性中的将* [X.509 CA 证书注册到 IoT 中心](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)部分中的说明进行操作。 在该部分中，执行以下步骤： 

   1. 上传根 CA 证书。 如果使用的是在透明网关一文中创建的证书，请上传 **\<WRKDIR >/certs/azure-iot-test-only.root.ca.cert.pem**作为根证书文件。 
   2. 验证你拥有此根 CA 证书。 你可以验证是否拥有 \<WRKDIR > 中的证书工具。 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. 按照在*Azure iot 中心设置 x.509 安全性*部分的为[IoT 中心创建 x.509 设备](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)部分中的说明进行操作。 在该部分中，执行以下步骤： 

   1. 添加新设备。 提供 "**设备 ID**" 的小写名称，然后选择 "身份验证类型**x.509 CA**"。 
   2. 设置父设备。 对于下游设备，请选择 "**设置父设备**"，然后选择将提供与 IoT 中心的连接的 IoT Edge 网关设备。 

3. 为下游设备创建证书链。 使用上载到 IoT 中心的相同根 CA 证书建立此链。 使用为门户中的设备标识提供的相同小写设备 ID。

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. 将以下文件复制到下游设备上的任何目录中： 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   你将在连接到 IoT 中心的叶设备应用程序中引用这些文件。 你可以使用诸如[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)之类的服务或类似于[安全复制协议](https://www.ssh.com/ssh/scp/)的函数来移动证书文件。

你可以使用[Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例使用 x.509 CA 签名身份验证创建新的 IoT 设备，并分配父设备： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

有关设备创建和父/子管理 Azure CLI 命令的详细信息，请参阅[az iot 中心设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令的参考内容。


### <a name="connect-to-iot-hub-through-a-gateway"></a>通过网关连接到 IoT 中心

每个 Azure IoT SDK 处理 x.509 身份验证的方式稍有不同。 但是，使用 x.509 证书对 IoT 中心的常规 IoT 设备进行身份验证的过程也适用于下游设备。 唯一的区别在于，你需要添加指向网关设备的指针来路由连接，或在脱机方案中添加以代表 IoT 中心处理身份验证。 通常，可以对所有 IoT 中心设备执行相同的 x.509 authentication 步骤，只需将连接字符串中**主机名**的值替换为网关设备的主机名。 

以下部分显示了不同 SDK 语言的一些示例。 

>[!IMPORTANT]
>以下示例演示了 IoT 中心 Sdk 如何使用证书对设备进行身份验证。 在生产部署中，应在硬件安全模块（HSM）中存储所有机密（如私有或 SAS 密钥）。 

#### <a name="net"></a>.NET

有关使用 x.509 证书向C# IoT 中心进行身份验证的程序的示例，请参阅[在 Azure IoT 中心设置 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)。 此处提供了此示例的部分关键行以演示身份验证过程。

声明 DeviceClient 实例的主机名时，请使用 IoT Edge 网关设备的主机名。 可在网关设备的 yaml 文件中找到该主机名。 

如果使用 IoT Edge git 存储库提供的测试证书，则证书的密钥为**1234**。

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

有关使用 x.509 证书向 IoT 中心进行身份验证的 C 程序的示例，请参阅 C IoT SDK 的[iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)示例。 此处提供了此示例的部分关键行以演示身份验证过程。

定义下游设备的连接字符串时，请使用 IoT Edge 的网关设备主机名作为**hostname**参数。 可在网关设备的 yaml 文件中找到该主机名。 

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

有关使用 x.509 证书向 IoT 中心进行身份验证的 node.js 程序示例，请参阅 node.js IoT SDK 的[simple_sample_device_x509](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)示例。 此处提供了此示例的部分关键行以演示身份验证过程。

定义下游设备的连接字符串时，请使用 IoT Edge 的网关设备主机名作为**hostname**参数。 可在网关设备的 yaml 文件中找到该主机名。 

如果使用 IoT Edge git 存储库提供的测试证书，则证书的密钥为**1234**。

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

定义下游设备的主机名时，请使用 IoT Edge 的网关设备主机名作为**hostname**参数。 可在网关设备的 yaml 文件中找到该主机名。 

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

有关使用 x.509 证书向 IoT 中心进行身份验证的 Java 程序的示例，请参阅 Java IoT SDK 的[SendEventX509](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)示例。 此处提供了此示例的部分关键行以演示身份验证过程。

定义下游设备的连接字符串时，请使用 IoT Edge 的网关设备主机名作为**hostname**参数。 可在网关设备的 yaml 文件中找到该主机名。 

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

完成本文后，应该有一个 IoT Edge 设备作为透明网关，并向 IoT 中心注册了下游设备。 接下来，需要将下游设备配置为信任网关设备，并向其发送消息。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。
