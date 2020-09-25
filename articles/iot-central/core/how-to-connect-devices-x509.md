---
title: 使用 Azure IoT Central 应用程序中的 x.509 证书连接设备
description: 如何使用 Node.js 设备 SDK IoT Central 应用程序将设备连接到 x.509 证书
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6de711567e87bcdd1e58185f90264d0c9aecdfde
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341515"
---
# <a name="how-to-connect-devices-with-x509-certificates-using-nodejs-device-sdk-for-iot-central-application"></a>如何使用 Node.js 设备 SDK IoT Central 应用程序将设备连接到 x.509 证书

IoT Central 支持共享访问签名 (SAS) 和 x.509 证书，以保护设备与应用程序之间的通信。 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device-nodejs.md)教程使用 SAS。 本文介绍如何修改代码示例以使用 x.509。  在生产环境中，建议使用 x.509 证书。 有关详细信息，请参阅 [连接到 Azure IoT Central](./concepts-get-connected.md)。

本文介绍了在生产环境 [中通常使用的 x.509 的两](how-to-connect-devices-x509.md#use-a-group-enrollment) 种使用方式的方法，以及用于测试的 [单个注册](how-to-connect-devices-x509.md#use-an-individual-enrollment) 。

## <a name="prerequisites"></a>先决条件

- 完成 [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序 ( # A0) ](./tutorial-connect-device-nodejs.md) 教程。
- [Git](https://git-scm.com/download/)。
- 下载并安装 [OpenSSL](https://www.openssl.org/)。 如果使用的是 Windows，则可以使用 [SourceForge 上的 OpenSSL 页面](https://sourceforge.net/projects/openssl/)中的二进制文件。

## <a name="use-a-group-enrollment"></a>使用组注册

在生产环境中将 x.509 证书与组注册一起使用。 在组注册中，将根或中间的 x.509 证书添加到 IoT Central 应用程序。 具有派生自根证书或中间证书的叶证书的设备可以连接到你的应用程序。


## <a name="generate-root-and-device-cert"></a>生成根证书和设备证书

在本部分中，你将使用 x.509 证书将设备与派生自注册组的证书的证书连接，该证书可以连接到你的 IoT Central 应用程序。

> [!WARNING]
> 这种生成 x.509 证书的方式只是为了进行测试。 对于生产环境，应该使用官方的安全机制来生成证书。

1. 打开命令提示符。 克隆证书生成脚本的 GitHub 存储库：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-node.git
    ```

2. 导航到证书生成器脚本并安装所需的包：

    ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    npm install
    ```

3. 创建根证书，然后通过运行脚本派生设备证书。 请确保对证书名称只使用小写字母数字和连字符。

    ```cmd/sh
    node create_test_cert.js root mytestrootcert
    node create_test_cert.js device mytestdevice mytestrootcert
    ```

这将为根和设备证书生成三个文件

filename | 内容
-------- | --------
\<name\>_cert pem | X509 证书的公共部分
\<name\>_key pem | X509 证书的私钥
\<name\>_fullchain pem | X509 证书的整个密钥链。


## <a name="create-a-group-enrollment"></a>创建组注册


1. 现在打开 IoT Central 应用程序，导航到左侧窗格中的 " **管理**  "，然后单击 " **设备连接**"。 

2. 选择 "+ **创建注册组**"，然后创建一个名为 _MyX509Group_ 的新注册组，其证明类型为 **证书 (x.509) **：


3. 打开创建的注册组，并单击 " **管理主要**"。 

4. 选择 "文件" 选项，然后上传之前生成的名为 _mytestrootcert_cert_ 的根证书文件：


    ![证书上传](./media/how-to-connect-devices-x509/certificate-upload.png)



5. 若要完成验证，请复制验证代码，并在命令提示符下使用该代码创建一个 x.509 验证证书。

    ```cmd/sh
    node create_test_cert.js verification --ca mytestrootcert_cert.pem --key mytestrootcert_key.pem --nonce  {verification-code}
    ```

6. 将签名的验证证书上传 _verification_cert_ 以完成验证。

    ![验证的证书](./media/how-to-connect-devices-x509/verified.png)


你现在可以连接具有派生自此主根证书的 x.509 证书的设备。 保存注册组后，记下 "ID" 范围。


## <a name="run-sample-device-code"></a>运行示例设备代码


1. 在 Azure IoT Central 应用程序中，单击 " **设备**"，并创建一个新设备，其中 _Mytestdevice_ 作为 "环境传感器" 设备模板中的 **设备 ID** 。


2. 完成 "[连接设备 ( # A1) 教程](./tutorial-connect-device-nodejs.md)时，请将_mytestdevice_key Pem_和_mytestdevice_cert_连接到包含_environmentalSensor.js_应用程序的文件夹。

3. 导航到包含 environmentalSensor.js 应用程序的文件夹，并运行以下命令安装 x.509 包：

    ```cmd/sh
    npm install azure-iot-security-x509 --save
    ```

4. 编辑 **environmentalSensor.js** 文件。
    - 将 `idScope` 值替换为你之前记下的**ID 范围** 
    - 将 `registrationId` value 替换为 `mytestdevice` 。

5. 编辑语句，如下所示 `require` ：

    ```javascript
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    var fs = require('fs');
    var X509Security = require('azure-iot-security-x509').X509Security;
    ```

6. 编辑创建客户端的部分，如下所示：

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var deviceCert = {
      cert: fs.readFileSync('mytestdevice_cert.pem').toString(),
      key: fs.readFileSync('mytestdevice_key.pem').toString()
    };
    var provisioningSecurityClient = new X509Security(registrationId, deviceCert);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    ```

7. 修改打开连接的部分，如下所示：

   ```javascript
    var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';x509=true';
    hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
    hubClient.setOptions(deviceCert);
    ```

8. 执行脚本，验证该设备是否已成功预配。

    ```cmd/sh
    node environmentalSensor.js
    ```   

    你还可以验证遥测显示在仪表板上。

    ![验证设备遥测](./media/how-to-connect-devices-x509/telemetry.png)

## <a name="use-an-individual-enrollment"></a>使用单个注册

使用 x.509 证书和单个注册来测试设备和解决方案。 在单个注册中，IoT Central 应用程序中没有根或中间的 x.509 证书。 设备使用自签名的 x.509 证书连接到应用程序。

## <a name="generate-self-signed-device-cert"></a>生成自签名设备证书


在本部分中，将使用自签名的 x.509 证书连接用于注册单个设备的单个注册设备。 自签名证书仅用于测试。

通过运行脚本创建自签名 x.509 设备证书。 请确保对证书名称只使用小写字母数字和连字符。

  ```cmd/sh
    cd azure-iot-sdk-node/provisioning/tools
    node create_test_cert.js device mytestselfcertprimary
    node create_test_cert.js device mytestselfcertsecondary 
  ```

## <a name="create-individual-enrollment"></a>创建单个注册

1. 在 Azure IoT Central 应用程序中，选择 " **设备**"，然后从环境传感器设备模板中创建 **设备 ID** 为 _mytestselfcertprimary_ 的新设备。 记下 **ID 范围**

2. 打开所创建的设备，然后选择 "**连接**"

3. 选择 " **单独注册** " 作为 Connect 方法，将 **证书 () ** 作为机制。

    ![单独注册](./media/how-to-connect-devices-x509/individual-device-connect.png)


4. 选择 "主文件" 下的 "文件" 选项，并上传之前生成的名为 _mytestselfcertprimary_cert_ 的证书文件。 

5. 选择辅助证书的文件选项，并上传名为 mytestselfcertsecondary_cert 的证书文件 _。_ 然后选择 "**保存**"

    ![上传单个注册证书](./media/how-to-connect-devices-x509/individual-enrollment.png)

设备现在预配了 x.509 证书。



## <a name="run-a-sample-individual-enrollment-device"></a>运行单个注册设备示例

1. 完成 "[连接设备 ( # A1) 教程](./tutorial-connect-device-nodejs.md)时，将_mytestselfcertprimary_key pem_和_mytestselfcertprimary_cert_复制到包含 environmentalSensor.js 应用程序的文件夹。


2. 按如下所示编辑 **environmentalSensor.js** 文件并保存。
    - 将 `idScope` 该值替换为之前记下的 **ID 范围** 。
    - 将 `registrationId` value 替换为 `mytestselfcertprimary` 。
    - 将 **Var deviceCert** 替换为：
    ```cmd\sh
    var deviceCert = {
    cert: fs.readFileSync('mytestselfcertprimary_cert.pem').toString(),
    key: fs.readFileSync('mytestselfcertprimary_key.pem').toString()
    };
    ```

3. 执行脚本，验证该设备是否已成功预配。

    ```cmd/sh
    node environmentalSensor.js
    ```   

    你还可以验证遥测显示在仪表板上。

    ![遥测单个注册](./media/how-to-connect-devices-x509/telemetry-primary.png)

还可以对 _mytestselfcertsecondary_ 证书重复上述步骤。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 x.509 证书连接设备，接下来建议的下一步是了解如何 [使用监视设备连接性 Azure CLI](howto-monitor-devices-azure-cli.md)

