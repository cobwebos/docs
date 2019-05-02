---
title: 创建透明网关设备 - Azure IoT Edge | Microsoft Docs
description: 将 Azure IoT Edge 用作可处理来自下游设备的消息的透明网关
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 722ee6197b467454818026c960e1ce0e5b39efb4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717193"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>将 IoT Edge 设备配置为充当透明网关

本文详细说明如何将 IoT Edge 设备配置为充当透明网关，供其他设备用来与 IoT 中心通信。 在本文中，术语“IoT Edge 网关”是指用作透明网关的 IoT Edge 设备。 有关详细信息，请参阅[如何将 IoT Edge 设备用作网关](./iot-edge-as-gateway.md)。

>[!NOTE]
>当前：
> * 如果网关与 IoT 中心断开连接，则下游设备无法通过网关进行身份验证。
> * 支持 Edge 的设备无法连接到 IoT Edge 网关。 
> * 下游设备不能使用文件上传。

充当网关的设备必须能够安全连接到下游设备。 Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游设备连接到充当透明网关的 IoT Edge 设备。 若要保持合理的安全性，下游设备应确认 IoT Edge 设备的标识。 你想连接到仅在网关，没有任何潜在的恶意的网关设备。

下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 在各种实际用途中，下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 在本文中，我们假定此证书的安装程序要用来启用[X.509 CA 安全性](../iot-hub/iot-hub-x509ca-overview.md)在 IoT 中心，涉及到特定的 IoT 中心 （IoT 中心所有者 CA） 和一系列的证书相关联的 X.509 CA 证书，使用此 CA 和 CA 签名的 IoT Edge 设备。

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

网关连接的初始化期间显示其 IoT Edge 设备到下游设备的 CA 证书。 下游设备检查以确保 IoT Edge 设备 CA 证书由所有者 CA 证书签名。 此过程允许下游设备确认网关是否来自受信任的源。

以下步骤将演示创建证书并将它们安装在正确位置的过程。

## <a name="prerequisites"></a>必备组件

要配置为网关的 Azure IoT Edge 设备。 对于以下操作系统中使用 IoT Edge 安装步骤：
* [Windows](./how-to-install-iot-edge-windows.md)
* [Linux x64](./how-to-install-iot-edge-linux.md)
* [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

可以使用任一计算机生成证书，然后将其复制到 IoT Edge 设备。

>[!NOTE]
>在此说明中用于创建证书的“网关名称”必须与 IoT Edge 的 config.yaml 文件中用作主机名的名称以及在下游设备的连接字符串中用作 GatewayHostName 的名称相同。 “网关名称”必须能够解析成 IP 地址，不管是使用 DNS 还是主机文件条目。 必须能够在下游设备和透明 IoT Edge 之间进行基于所使用协议 (MQTTS:8883/AMQPS:5671/HTTPS:433) 的通信。 如果中间有防火墙，则需打开相应的端口。

## <a name="generate-certificates-with-windows"></a>在 Windows 中生成证书

使用本部分中的步骤在 Windows 设备上生成测试证书。 这些步骤可用于生成 Windows IoT Edge 设备上的证书。 或者，可以在 Windows 开发计算机上生成的证书，然后将它们复制到任何 IoT Edge 设备。 

在本部分中生成的证书仅用于测试目的。 

### <a name="install-openssl"></a>安装 OpenSSL

在用于生成证书的计算机上安装 OpenSSL for Windows。 可通过多种方法安装 OpenSSL：

   >[!NOTE]
   >如果已在 Windows 设备上安装 OpenSSL，则可以跳过此步骤，但请确保 PATH 环境变量中包含 openssl.exe。

* **更轻松：** 下载并安装任何[第三方 OpenSSL 二进制文件](https://wiki.openssl.org/index.php/Binaries)，例如从 [SourceForge 上的此项目](https://sourceforge.net/projects/openssl/)下载并安装。 将 openssl.exe 的完整路径添加到 PATH 环境变量。 
   
* **推荐：** 在计算机上下载 OpenSSL 源代码并自行生成二进制文件，或者通过 [vcpkg](https://github.com/Microsoft/vcpkg) 生成。 下面列出的说明使用 vcpkg 下载源代码，并在 Windows 计算机上编译和安装 OpenSSL，所用的步骤都很简单。

   1. 导航到要安装 vcpkg 的目录。 我们将此目录称作 *\<VCPKGDIR>*。 按照说明下载并安装 [vcpkg](https://github.com/Microsoft/vcpkg)。
   
   2. 安装 vcpkg 后，在 powershell 提示符下运行以下命令以安装适用于 Windows x64 的 OpenSSL 包。 此安装通常需要大约 5 分钟才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. 将 `<VCPKGDIR>\installed\x64-windows\tools\openssl` 添加到 PATH 环境变量，以便可以调用 openssl.exe 文件。

### <a name="prepare-creation-scripts"></a>准备创建脚本

适用于 C 的 Azure IoT 设备 SDK 包含可用于生成测试证书的脚本。 在本部分，我们将克隆该 SDK 并配置 PowerShell。

1. 在管理员模式下打开 PowerShell 窗口。 

2. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 使用 `git clone` 命令或[下载 ZIP](https://github.com/Azure/azure-iot-sdk-c/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

3. 导航到要在其中工作的目录。 我们将此目录称作 *\<WRKDIR>*。  所有文件都将在此目录中创建。

4. 将配置文件和脚本文件复制到工作目录中。 

   ```powershell
   copy <path>\azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy <path>\azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   ```

   如果下载了 ZIP 格式的 SDK，则文件夹名称为 `azure-iot-sdk-c-master`，路径的剩余部分相同。 

5. 将环境变量 OPENSSL_CONF 设置为使用 openssl_root_ca.cnf 配置文件。

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```

6. 启用 PowerShell 以运行脚本。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. 将脚本使用的函数放入 PowerShell 全局命名空间中。
   
   ```powershell
   . .\ca-certs.ps1
   ```

8. 验证 OpenSSL 是否已正确安装，并确保现有的证书不会发生名称冲突。 如果出现问题，脚本应说明如何在系统上修复这些问题。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>创建证书

在本部分，我们将创建三个证书，然后将它们连接起来以形成一个链。 将证书放入链文件可在 IoT Edge 网关设备和任何下游设备上轻松安装这些证书。  

1. 创建所有者 CA 证书，并使用它来签署一个中间证书。 所有证书都位于 *\<WRKDIR>* 中。

      ```powershell
      New-CACertsCertChain rsa
      ```

2. 使用以下命令创建 IoT Edge 设备的 CA 证书和私钥。 提供网关设备的名称，在生成证书期间，此名称将用来为文件命名。 

   ```powershell
   New-CACertsEdgeDevice "<gateway name>"
   ```

3. 从所有者 CA 证书、 中间证书和 IoT Edge 设备 CA 证书，使用以下命令创建证书链。 

   ```powershell
   Write-CACertsCertificatesForEdgeDevice "<gateway name>"
   ```

   该脚本创建以下证书和密钥：
   * `<WRKDIR>\certs\new-edge-device.*`
   * `<WRKDIR>\private\new-edge-device.key.pem`
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="generate-certificates-with-linux"></a>在 Linux 中生成证书

使用本部分中的步骤在 Linux 设备上生成测试证书。 可以在 IoT Edge 设备本身上生成证书，或使用独立的计算机，并将最终的证书复制到运行任何受支持操作系统的任何 IoT Edge 设备。 

### <a name="prepare-creation-scripts"></a>准备创建脚本

1. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 

   ```bash
   git clone https://github.com/Azure/azure-iot-sdk-c.git
   ```

2. 导航到要在其中工作的目录。 我们将此目录称作 *\<WRKDIR>*。  所有文件都将在此目录中创建。
  
3. 将配置文件和脚本文件复制到工作目录中。

   ```bash
   cp <path>/azure-iot-sdk-c/tools/CACertificates/*.cnf .
   cp <path>/azure-iot-sdk-c/tools/CACertificates/certGen.sh .
   ```

4. 将 OpenSSL 配置为使用提供的脚本生成证书。 

   ```bash
   chmod 700 certGen.sh 
   ```

### <a name="create-certificates"></a>创建证书

在本部分，我们将创建三个证书，然后将它们连接起来以形成一个链。 将证书放入链文件可在 IoT Edge 网关设备和任何下游设备上轻松安装这些证书。  

1. 创建所有者 CA 证书和一个中间证书。 这些证书位于 *\<WRKDIR>* 中。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   该脚本创建以下证书和密钥：
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>/certs/azure-iot-test-only.intermediate.cert.pem`
   * `<WRKDIR>/private/azure-iot-test-only.root.ca.key.pem`
   * `<WRKDIR>/private/azure-iot-test-only.intermediate.key.pem`

2. 使用以下命令创建 IoT Edge 设备的 CA 证书和私钥。 提供网关设备的名称，在生成证书期间，此名称将用来为文件命名。 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway name>"
   ```

   该脚本创建以下证书和密钥：
   * `<WRKDIR>/certs/new-edge-device.*`
   * `<WRKDIR>/private/new-edge-device.key.pem`

3. 创建名为的证书链**新的 edge 的设备的完整-chain.cert.pem**从所有者 CA 证书、 中间证书和 IoT Edge 设备 CA 证书。

   ```bash
   cat ./certs/new-edge-device.cert.pem ./certs/azure-iot-test-only.intermediate.cert.pem ./certs/azure-iot-test-only.root.ca.cert.pem > ./certs/new-edge-device-full-chain.cert.pem
   ```

## <a name="install-certificates-on-the-gateway"></a>在网关上安装证书

创建证书链后，需要在 IoT Edge 网关设备上安装它，并将 IoT Edge 运行时配置为引用新证书。 

1. 复制 *\<WRKDIR>* 中的以下文件。 将其保存在 IoT Edge 设备上的任意位置。 我们将 IoT Edge 设备上的目标目录称作 *\<CERTDIR>*。 

   如果生成 IoT Edge 设备本身上的证书，则可以跳过此步骤并使用工作目录的路径。

   * 设备 CA 证书 - `<WRKDIR>\certs\new-edge-device-full-chain.cert.pem`
   * 设备 CA 私钥 - `<WRKDIR>\private\new-edge-device.key.pem`
   * 所有者 CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 打开 IoT Edge 安全守护程序配置文件。 

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 将 config.yaml 文件中的 **certificate** 属性设置为 IoT Edge 设备上放置证书和密钥文件的路径。

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux： 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/new-edge-device-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/new-edge-device.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. 在 Linux 设备，请确保用户**iotedge**已读取将证书保存目录的权限。 

## <a name="deploy-edgehub-to-the-gateway"></a>将 EdgeHub 部署到网关

首次在设备上安装 IoT Edge，只有一个系统模块会自动启动： IoT Edge 代理。 要使设备充当网关，需要两个系统模块。 如果你尚未部署到网关设备之前的所有模块，创建你的设备以开始第二个系统模块的 IoT Edge 中心的部署。 该部署看上去是空的，因为尚未在向导中添加任何模块，但它会部署这两个系统模块。 

可以使用 `iotedge list` 命令检查哪些模块正在设备上运行。

1. 在 Azure 门户中导航到 IoT 中心。

2. 转到“IoT Edge”并选择要用作网关的 IoT Edge 设备。

3. 选择“设置模块”。

4. 选择“**下一步**”。

5. 在“指定路由”页中，应有一个默认路由可将来自所有模块的所有消息发送到 IoT 中心。 如果没有，请添加以下代码，然后选择“下一步”。

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. 在“审阅模板”页中选择“提交”。

## <a name="open-ports-on-gateway-device"></a>网关设备上打开端口

标准的 IoT Edge 设备不需要任何入站的连接到函数，因为与 IoT 中心的所有通信均都通过出站连接。 但是，网关设备则不同，因为它们需要能够从其下游设备接收消息。

若要运行的网关方案，在至少一个 IoT Edge 中心支持的协议必须打开从下游设备的入站流量。 支持的协议为 MQTT、 AMQP 和 HTTPS。

| 端口 | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>路由来自下游设备的消息
IoT Edge 运行时可以像模块发送的消息一样路由从下游设备发送的消息。 此功能，可将任何数据发送到云之前在网关上运行模块中执行分析。 

目前，对由下游设备发送的消息进行路由的方式是将它们与由模块发送的消息区分开来。 由模块发送的消息全都包含名为“connectionModuleId”的系统属性，但由下游设备发送的消息则不包含此属性。 可以使用路由的 WHERE 子句以排除包含该系统属性的任何消息。 

以下路由将用于将消息从任何下游设备发送到模块名称 `ai_insights`。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

有关消息路由的详细信息，请参阅[部署模块和建立路由](./module-composition.md#declare-routes)。

[!INCLUDE [iot-edge-extended-ofline-preview](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>后续步骤

配置一个充当透明网关的 IoT Edge 设备后，需将下游设备配置为信任该网关，并向其发送消息。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。
