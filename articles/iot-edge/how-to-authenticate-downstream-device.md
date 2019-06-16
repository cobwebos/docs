---
title: 对下游设备-Azure IoT Edge 进行身份验证 |Microsoft Docs
description: 了解如何对下游设备或叶设备到 IoT 中心进行身份验证并将其连接路由通过 Azure IoT Edge 网关设备。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082386"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>到 Azure IoT 中心下游设备进行身份验证

在透明网关方案中，下游设备 （有时称为叶设备或子设备） 需要 IoT 中心中像任何其他设备的标识。 本文将指导完成对下游设备到 IoT 中心进行身份验证的选项，然后演示如何声明网关连接。

有三个常规步骤来设置成功的透明网关连接。 本文介绍第二个步骤：

1. 网关设备都需要能够安全地连接到下游设备、 从下游设备接收的通信和将消息路由到正确的目的地。 有关详细信息，请参阅[配置为充当透明网关的 IoT Edge 设备](how-to-create-transparent-gateway.md)。
2. **下游设备需要有设备标识，以便能够使用 IoT 中心进行身份验证和了解通过其网关设备进行通信。**
3. 下游设备都需要能够安全地连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

下游设备可以使用 IoT 中心使用三种方法之一进行身份验证: （有时称为共享的访问密钥） 的对称密钥、 X.509 自签名的证书或 X.509 证书颁发机构 (CA) 签名的证书。 身份验证步骤是类似于用于设置小差异，以声明的网关关系与任何非 IoT Edge 的设备与 IoT 中心的步骤。

在本文中的步骤显示手动设备预配，不自动预配 Azure IoT 中心设备预配服务。 

## <a name="symmetric-key-authentication"></a>对称密钥身份验证

对称密钥的身份验证或共享的访问密钥身份验证，使用 IoT 中心进行身份验证的最简单方法。 使用对称密钥的身份验证，base64 密钥是与你在 IoT 中心的 IoT 设备 ID 相关联。 以便你的设备连接到 IoT 中心时可以显示它，可以在 IoT 应用程序中包括该注册表项。 

### <a name="create-the-device-identity"></a>创建设备标识 

在 IoT 中心，使用用于 Visual Studio Code 的 Azure 门户、 Azure CLI 或 IoT 扩展中添加新的 IoT 设备。 请记住，下游设备需要在 IoT 中心标识为常规 IoT 设备，不是 IoT Edge 设备。 

在创建新的设备标识时，提供以下信息： 

* 创建你的设备的 ID。

* 选择**对称密钥**作为身份验证类型。 

* （可选） 选择**设置父设备**并选择此下游设备将通过连接的 IoT Edge 网关设备。 此步骤是可选的对称密钥的身份验证，但建议设置父设备后，因为[离线功能](offline-capabilities.md)下游设备。 始终可以更新设备详细信息，若要添加或更改父级更高版本。 

   ![使用对称密钥的身份验证门户中创建设备 ID](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

可以使用[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同操作。 以下示例使用对称密钥身份验证将创建一个新的 IoT 设备，并将父设备： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

用于创建设备和父/子管理的 Azure CLI 命令的详细信息，请参阅有关的参考内容[az iot 中心设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令。

### <a name="connect-to-iot-hub-through-a-gateway"></a>通过网关连接到 IoT 中心

使用相同的过程进行身份验证常规 IoT 设备到 IoT 中心使用对称密钥也适用于下游设备。 唯一的区别是，您需要添加一个指针，将连接路由的网关设备，或在脱机情况下，以处理 IoT 中心代表身份验证。 

对于对称密钥的身份验证，没有需要使用 IoT 中心，以便进行身份验证在设备上执行任何其他步骤。 您仍需要的位置中的证书，以便在下游设备可以连接到其网关设备，如中所述[下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。

在门户中创建的 IoT 设备标识之后, 可以检索其主密钥或辅助密钥。 这些项需要包含在与 IoT 中心通信，任何应用程序中包含的连接字符串。 对于对称密钥的身份验证，IoT 中心为方便起见提供设备详细信息中的完整的连接字符串。 您需要将网关设备的额外信息添加到连接字符串。 

下游设备的对称密钥连接字符串需要以下组件： 

* 设备连接到 IoT 中心： `Hostname={iothub name}.azure-devices.net`
* 在中心注册的设备 ID: `DeviceID={device ID}`
* 主要或辅助密钥： `SharedAccessKey={key}`
* 通过连接设备与网关设备。 提供**主机名**IoT Edge 网关设备 config.yaml 文件中的值： `GatewayHostName={gateway hostname}`

它们组合在一起的完整连接字符串如下所示：

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

如果建立此下游设备的父/子关系，然后可以通过调用直接连接主机作为网关简化连接字符串。 例如： 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>X.509 身份验证 

有两种方法对 IoT 设备使用 X.509 证书进行身份验证。 哪种方式选择进行身份验证，以将设备连接到 IoT 中心的步骤相同。 选择自签名或 CA 签名证书进行身份验证，然后继续了解如何连接到 IoT 中心。 

有关 IoT 中心如何使用 X.509 身份验证的详细信息，请参阅以下文章： 
* [使用 X.509 CA 证书进行设备身份验证](../iot-hub/iot-hub-x509ca-overview.md)
* [概念性理解 IoT 行业中的 X.509 CA 证书](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>使用自签名的 X.509 证书创建设备标识

对于 X.509 自签名身份验证，有时称为指纹身份验证，需要创建新的证书放置在 IoT 设备上。 这些证书具有指纹，在其中使用 IoT 中心共享进行身份验证。 

若要测试此方案中的最简单方法是使用用于创建中的证书在同一台计算机[配置为充当透明网关的 IoT Edge 设备](how-to-create-transparent-gateway.md)。 该计算机应已设置使用合适的工具、 根 CA 证书和中间 CA 证书创建的 IoT 设备证书。 您可以将最终证书及私钥复制到你的下游设备之后。 网关文章中的步骤，按照您在计算机上设置了 openssl，然后访问证书创建脚本到 IoT Edge 存储库克隆。 然后，你所做的工作目录，我们称之为 **\<WRKDIR >** 来保存证书。 默认证书用于开发和测试，因此仅过去 30 天。 您应已创建的根 CA 证书和中间证书。 

1. 导航到工作目录中的 bash 或 PowerShell 窗口。 

2. 创建下游设备的两个证书 （主要和辅助）。 提供设备名称，然后主要或次要标签。 此信息用于命名文件，以便你可以跟踪的多个设备的证书。 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. 检索每个证书，这是 40 个十六进制字符字符串 （在 IoT 中心界面中称为指纹） 的 SHA1 指纹。 使用以下 openssl 命令来查看证书，并查找指纹：

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. 导航到 IoT 中心在 Azure 门户中，使用以下值创建新的 IoT 设备标识： 

   * 选择**自签名的 X.509**作为身份验证类型。
   * 粘贴从你的设备的主要和辅助证书中复制的十六进制字符串。
   * 选择**设置父设备**并选择此下游设备将通过连接的网关设备的 IoT Edge。 父设备是必需的下游设备的 X.509 身份验证。 

   ![使用 X.509 自签名身份验证在门户中创建设备 ID](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. 将以下文件复制到下游设备上的任何目录：

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   将引用这些文件中的叶设备应用程序连接到 IoT 中心。 可以使用之类的服务[Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault)或其功能类似于[安全的复制上](https://www.ssh.com/ssh/scp/)移动的证书文件。

可以使用[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例使用自签名 X.509 的身份验证将创建一个新的 IoT 设备，并将父设备： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

用于创建设备、 证书生成和父级和子级管理的 Azure CLI 命令的详细信息，请参阅有关的参考内容[az iot 中心设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令。

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>创建设备标识与 X.509 CA 签名证书

对于 X.509 证书颁发机构 (CA) 签名的身份验证，需要在使用 IoT 设备的证书进行签名的 IoT 中心中注册的根 CA 证书。 将允许使用由根 CA 证书或任何其中间证书是问题的证书的任何设备进行身份验证。 

本部分基于 IoT 中心文章中详述的说明[设置中的 Azure IoT 中心 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)。 按照此部分以了解哪些值用于设置通过网关连接的下游设备中的步骤。 

若要测试此方案的最简单方法是使用用于创建中的证书在同一台计算机[配置为充当透明网关的 IoT Edge 设备](how-to-create-transparent-gateway.md)。 该计算机应已设置使用合适的工具、 根 CA 证书和中间 CA 证书创建的 IoT 设备证书。 您可以将最终证书及私钥复制到你的下游设备之后。 网关文章中的步骤，按照您在计算机上设置了 openssl，然后访问证书创建脚本到 IoT Edge 存储库克隆。 然后，你所做的工作目录，我们称之为 **\<WRKDIR >** 来保存证书。 默认证书用于开发和测试，因此仅过去 30 天。 您应已创建的根 CA 证书和中间证书。 

1. 按照中的说明[到 IoT 中心注册 X.509 CA 证书](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)一部分*设置中的 Azure IoT 中心 X.509 安全性*。 在该部分中，您将执行以下步骤： 

   1. 上传的根 CA 证书。 如果使用透明网关文章中创建的证书，将上传 **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem**作为根证书文件。 
   2. 验证你拥有该根 CA 证书。 你可以验证证书中的工具拥有\<WRKDIR >。 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. 按照中的说明[创建 X.509 设备的 IoT 中心](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub)一部分*设置中的 Azure IoT 中心 X.509 安全性*。 在该部分中，您将执行以下步骤： 

   1. 添加新设备。 提供的小写名称**设备 ID**，然后选择身份验证类型**X.509 CA 签名**。 
   2. 父设备设置。 对于下游设备，选择**设置父设备**并选择 IoT Edge 将提供连接到 IoT 中心的网关设备。 

3. 创建你的下游设备的证书链。 使用相同的根 CA 证书上传到 IoT 中心，使此链。 使用您提供给你在门户中的设备身份的同一个小写的设备 ID。

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. 将以下文件复制到下游设备上的任何目录： 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   将引用这些文件中的叶设备应用程序连接到 IoT 中心。 可以使用之类的服务[Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault)或其功能类似于[安全的复制上](https://www.ssh.com/ssh/scp/)移动的证书文件。

可以使用[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)来完成相同的设备创建操作。 以下示例创建一个新的 IoT 设备与 X.509 CA 签名的身份验证，并将父设备： 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

用于创建设备和父/子管理的 Azure CLI 命令的详细信息，请参阅有关的参考内容[az iot 中心设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)命令。


### <a name="connect-to-iot-hub-through-a-gateway"></a>通过网关连接到 IoT 中心

每个 Azure IoT SDK 略有不同处理 X.509 身份验证。 但是，使用相同的过程进行身份验证常规 IoT 设备到 IoT 中心使用 X.509 证书也适用于下游设备。 唯一的区别是，您需要添加一个指针，将连接路由的网关设备，或在脱机情况下，以处理 IoT 中心代表身份验证。 一般情况下，你可以按照相同的 X.509 身份验证步骤对于所有 IoT 中心设备，然后只需替换的值**主机名**中为你网关设备的主机名的连接字符串。 

以下各节介绍不同的 SDK 语言的一些示例。 

>[!IMPORTANT]
>以下示例演示了 IoT 中心 Sdk 如何使用证书进行身份验证的设备。 在生产部署中，应存储私有或硬件安全模块 (HSM) 中的 SAS 密钥等的所有机密。 

#### <a name="net"></a>.NET

有关示例的C#程序进行到 IoT 中心使用 X.509 证书的身份验证，请参阅[设置中的 Azure IoT 中心 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)。 该示例的关键代码行的部分是此处包括来演示该身份验证过程。

当声明 DeviceClient 实例的主机名时，使用 IoT Edge 网关设备主机名。 网关设备 config.yaml 文件中可以找到主机名。 

如果您使用提供的 IoT Edge git 存储库的测试证书，证书的关键在于**1234年**。

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

有关 C 程序中的示例进行到 IoT 中心使用 X.509 证书的身份验证看到 C IoT SDK [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample)示例。 该示例的关键代码行的部分是此处包括来演示该身份验证过程。

在定义为下游设备连接字符串时，使用 IoT Edge 网关设备主机名**主机名**参数。 网关设备 config.yaml 文件中可以找到主机名。 

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

有关 Node.js 程序的示例进行到 IoT 中心使用 X.509 证书的身份验证查看 Node.js IoT SDK [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)示例。 该示例的关键代码行的部分是此处包括来演示该身份验证过程。

在定义为下游设备连接字符串时，使用 IoT Edge 网关设备主机名**主机名**参数。 网关设备 config.yaml 文件中可以找到主机名。 

如果您使用提供的 IoT Edge git 存储库的测试证书，证书的关键在于**1234年**。

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

有关 Python 程序的示例进行到 IoT 中心使用 X.509 证书的身份验证查看 Java IoT SDK [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)示例。 该示例的关键代码行的部分是此处包括来演示该身份验证过程。

在定义为下游设备连接字符串时，使用 IoT Edge 网关设备主机名**主机名**参数。 网关设备 config.yaml 文件中可以找到主机名。 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)

def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

有关 Java 程序的示例进行到 IoT 中心使用 X.509 证书的身份验证查看 Java IoT SDK [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py)示例。 该示例的关键代码行的部分是此处包括来演示该身份验证过程。

在定义为下游设备连接字符串时，使用 IoT Edge 网关设备主机名**主机名**参数。 网关设备 config.yaml 文件中可以找到主机名。 

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

通过完成本文，您应具有 IoT Edge 设备用作透明网关正常工作并且下游设备注册到 IoT 中心。 接下来，您需要配置下游设备信任网关设备，并向其发送消息。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。
