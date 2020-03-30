---
title: 创建测试证书 - Azure IoT Edge | Microsoft Docs
description: 创建测试证书，并了解如何将其安装在 Azure IoT Edge 设备上以准备生产部署。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5afb9b7a6ba1ffb99df064c9f92780dc820b2e8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535980"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>创建演示证书用于测试 IoT Edge 设备功能

IoT Edge 设备需要使用证书来保护运行时、模块和任何下游设备之间的通信。
如果你没有可创建所需证书的证书颁发机构，可以在测试环境中使用演示证书来试用 IoT Edge 功能。
本文介绍 IoT Edge 提供用于测试的证书生成脚本的功能。

这些证书将在 30 天后过期，不应在任何生产方案中使用。

可以在任何计算机上创建证书，然后将其复制到 IoT Edge 设备。
使用主计算机创建证书比在 IoT Edge 设备本身上生成证书要方便一些。
使用主计算机可以设置脚本一次，然后重复该过程以创建多个设备的证书。

按照以下步骤创建用于测试 IoT Edge 方案的演示证书：

1. 为设备上的证书生成[设置脚本](#set-up-scripts)。
2. [创建](#create-root-ca-certificate)用于对方案的所有其他证书进行签名的根 CA 证书。
3. 生成要测试的方案所需的证书：
   * [创建 IoT 边缘设备标识证书](#create-iot-edge-device-identity-certificates)，以使用 IoT 中心设备配置服务测试自动预配。
   * [创建 IoT Edge 设备 CA 证书](#create-iot-edge-device-ca-certificates)以测试生产方案或网关方案。
   * [创建下游设备证书](#create-downstream-device-certificates)，以在网关方案中测试向 IoT 中心验证下游设备。

## <a name="prerequisites"></a>先决条件

装有 Git 的开发计算机。

## <a name="set-up-scripts"></a>设置脚本

GitHub 上的 IoT Edge 存储库包含可用于创建演示证书的证书生成脚本。
本部分说明如何准备好可在 Windows 或 Linux 计算机上运行的脚本。
如果使用的是 Linux 计算机，请跳到[在 Linux 上进行设置](#set-up-on-linux)。

### <a name="set-up-on-windows"></a>在 Windows 上进行设置

若要在 Windows 设备上创建演示证书，需要安装 OpenSSL，然后克隆生成脚本，并将其设置为在 PowerShell 中本地运行。

#### <a name="install-openssl"></a>安装 OpenSSL

在用于生成证书的计算机上安装 OpenSSL for Windows。
如果已在 Windows 设备上安装 OpenSSL，则可以跳过此步骤，但请确保 PATH 环境变量中包含 openssl.exe。

可通过多种方式安装 OpenSSL，包括以下选项：

* **更简单：** 下载并安装任何[第三方 OpenSSL 二进制文件](https://wiki.openssl.org/index.php/Binaries)，例如，从[SourceForge 上的 OpenSSL](https://sourceforge.net/projects/openssl/)下载和安装。 将 openssl.exe 的完整路径添加到 PATH 环境变量。

* **建议方法：** 在计算机上下载 OpenSSL 源代码并自行生成二进制文件，或者通过 [vcpkg](https://github.com/Microsoft/vcpkg) 生成。 下面列出的说明使用 vcpkg 下载源代码，并在 Windows 计算机上编译和安装 OpenSSL，所用的步骤都很简单。

   1. 导航到要安装 vcpkg 的目录。 按照说明下载并安装 [vcpkg](https://github.com/Microsoft/vcpkg)。

   2. 安装 vcpkg 后，请从 PowerShell 提示符运行以下命令，以安装适用于 Windows x64 的 OpenSSL 包。 此安装通常需要大约 5 分钟才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. 将 `<vcpkg path>\installed\x64-windows\tools\openssl` 添加到 PATH 环境变量，以便可以调用 openssl.exe 文件。

#### <a name="prepare-scripts-in-powershell"></a>在 PowerShell 中准备脚本

Azure IoT Edge Git 存储库包含可用于生成测试证书的脚本。
在本部分，你将克隆 IoT Edge 存储库并执行脚本。

1. 在管理员模式下打开 PowerShell 窗口。

2. 克隆 IoT Edge Git 存储库，其中包含用于生成演示证书的脚本。 使用 `git clone` 命令或[下载 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 导航到要在其中工作的目录。 在本文中，我们将调用此目录*\<WRKDIR>*。 所有证书和密钥将此工作目录中创建。

4. 将克隆的存储库中的配置文件和脚本文件复制到该工作目录。

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果下载了 ZIP 格式的存储库，则文件夹名称为 `iotedge-master`，路径的剩余部分相同。

5. 启用 PowerShell 以运行脚本。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. 将脚本使用的函数放入 PowerShell 全局命名空间中。

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 窗口中会显示一条警告，指出此脚本生成的证书仅用于测试目的，不应在生产方案中使用。

7. 验证 OpenSSL 是否已正确安装，并确保现有的证书不会发生名称冲突。 如果出现问题，脚本输出应会说明如何在系统上修复这些问题。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>在 Linux 上进行设置

若要在 Windows 设备上创建演示证书，需要克隆生成脚本，并将其设置为在 bash 中本地运行。

1. 克隆 IoT Edge Git 存储库，其中包含用于生成演示证书的脚本。

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 导航到要在其中工作的目录。 我们将在整个文章中引用此目录作为*\<WRKDIR>*。 所有证书和密钥文件都将在此目录中创建。
  
3. 将克隆的 IoT Edge 存储库中的配置文件和脚本文件复制到该工作目录。

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>创建根 CA 证书

根 CA 证书用于生成所有其他演示证书来测试 IoT Edge 方案。
可以保持使用同一个根 CA 证书来为多个 IoT Edge 设备或下游设备生成演示证书。

如果工作文件夹中已包含一个根 CA 证书，请不要创建新的根 CA 证书。
新根 CA 证书将覆盖旧证书，基于旧证书生成的任何下游证书将停止工作。
如果需要多个根 CA 证书，请务必在单独的文件夹中管理这些证书。

在继续执行本部分所述的步骤之前，请遵循[设置脚本](#set-up-scripts)部分中的步骤使用演示证书生成脚本准备好工作目录。

### <a name="windows"></a>Windows

1. 导航到在其中放置了证书生成脚本的工作目录。

1. 创建根 CA 证书，并使用它来签署一个中间证书。 证书全部放在工作目录中。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此脚本命令将创建多个证书和密钥文件，但当文章中提到**根 CA 证书**时，请使用以下文件：

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. 导航到在其中放置了证书生成脚本的工作目录。

1. 创建根 CA 证书和一个中间证书。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   此脚本命令将创建多个证书和密钥文件，但当文章中提到**根 CA 证书**时，请使用以下文件：

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>创建 IoT Edge 设备 CA 证书

投放到生产环境的每个 IoT Edge 设备都需要一个从 config.yaml 文件引用的设备 CA 证书。
设备 CA 证书负责为设备上运行的模块创建证书。
IoT Edge 设备在连接到下游设备时，也要通过此证书验证自身的身份。

设备 CA 证书位于 IoT 边缘设备上 config.yaml 文件的 **"证书"** 部分中。

在继续执行本部分所述的步骤之前，请执行[设置脚本](#set-up-scripts)和[创建根 CA 证书](#create-root-ca-certificate)部分所述的步骤。

### <a name="windows"></a>Windows

1. 导航到包含证书生成脚本和根 CA 证书的工作目录。

2. 使用以下命令创建 IoT Edge 设备 CA 证书和私钥。 提供 CA 证书的名称（例如 **MyEdgeDeviceCA**），用于对输出文件命名。

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   此脚本命令将创建多个证书和密钥文件。 以下证书和密钥对需要复制到 IoT Edge 设备，并在 config.yaml 文件中引用：

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

传递到这些脚本中的网关设备名称不应与 config.yaml 中的"主机名"参数或 IoT 中心中的设备的 ID 相同。
脚本将“.ca”字符串追加到 网关设备名称 以防止用户在两个位置使用相同的名称来设置 IoT Edge 时出现名称冲突，从而帮助避免产生任何问题。
但最好避免使用相同的名称。

### <a name="linux"></a>Linux

1. 导航到包含证书生成脚本和根 CA 证书的工作目录。

2. 使用以下命令创建 IoT Edge 设备 CA 证书和私钥。 提供 CA 证书的名称（例如 **MyEdgeDeviceCA**），用于对输出文件命名。

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   此脚本命令将创建多个证书和密钥文件。 以下证书和密钥对需要复制到 IoT Edge 设备，并在 config.yaml 文件中引用：

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

传递到这些脚本中的网关设备名称不应与 config.yaml 中的"主机名"参数或 IoT 中心中的设备的 ID 相同。
脚本将“.ca”字符串追加到 网关设备名称 以防止用户在两个位置使用相同的名称来设置 IoT Edge 时出现名称冲突，从而帮助避免产生任何问题。
但最好避免使用相同的名称。

## <a name="create-iot-edge-device-identity-certificates"></a>创建 IoT 边缘设备标识证书

设备标识证书用于通过[Azure IoT 中心设备配置服务 （DPS）](../iot-dps/index.yml)预配 IoT Edge 设备。

设备标识证书位于 IoT Edge 设备上的 config.yaml 文件的**预配**部分中。

在继续执行本部分所述的步骤之前，请执行[设置脚本](#set-up-scripts)和[创建根 CA 证书](#create-root-ca-certificate)部分所述的步骤。

### <a name="windows"></a>Windows

使用以下命令创建 IoT Edge 设备标识证书和私钥：

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

传递给此命令的名称将是 IoT 中心中的 IoT 边缘设备的设备 ID。

新的设备标识命令创建多个证书和密钥文件，包括两个在 DPS 中创建单个注册和安装 IoT Edge 运行时时将使用的文件：

* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

使用以下命令创建 IoT Edge 设备标识证书和私钥：

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

传递给此命令的名称将是 IoT 中心中的 IoT 边缘设备的设备 ID。

该脚本创建多个证书和密钥文件，包括两个在 DPS 中创建单个注册和安装 IoT Edge 运行时时将使用的文件：

* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>创建下游设备证书

为网关方案设置下游 IoT 设备时，可以生成用于 X.509 身份验证的演示证书。
可通过两种方式使用 X.509 证书对 IoT 设备进行身份验证：使用自签名证书，或使用证书颁发机构 (CA) 签名的证书。
对于 X.509 自签名身份验证（有时称为指纹身份验证），需要创建新证书并将其放在 IoT 设备上。
这些证书包含一个指纹，你可以与 IoT 中心共享该指纹以完成身份验证。
对于 X.509 证书颁发机构 (CA) 签名的身份验证，需要在 IoT 中心注册一个用来为 IoT 设备证书签名的根 CA 证书。
使用根 CA 证书或其任何中间证书颁发的证书的任何设备都可进行身份验证。

证书生成脚本可帮助你生成演示证书，以测试上述任一身份验证方案。

在继续执行本部分所述的步骤之前，请执行[设置脚本](#set-up-scripts)和[创建根 CA 证书](#create-root-ca-certificate)部分所述的步骤。

### <a name="self-signed-certificates"></a>自签名证书

使用自签名证书对 IoT 设备进行身份验证时，需要基于解决方案的根 CA 证书创建设备证书。
然后，从要提供给 IoT 中心的证书中检索十六进制“指纹”。
IoT 设备还需要其设备证书的副本，以便可以在 IoT 中心进行身份验证。

#### <a name="windows"></a>Windows

1. 导航到包含证书生成脚本和根 CA 证书的工作目录。

2. 为下游设备创建两个证书（主要和辅助）。 要使用的简易命名约定是依次使用 IoT 设备的名称以及主要标签或辅助标签创建证书。 例如：

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   此脚本命令将创建多个证书和密钥文件。 以下证书和密钥对需要复制到下游 IoT 设备，并在连接到 IoT 中心的应用程序中引用：

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. 从每个证书中检索 SHA1 指纹（在 IoT 中心上下文中称为“指纹”）。 指纹是由 40 个十六进制字符组成的字符串。 使用以下 openssl 命令查看证书并查找指纹：

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   运行此命令两次，一次针对主要证书，另一次针对辅助证书。 使用自签名的 X.509 证书注册新 IoT 设备时，为这两个证书提供指纹。

#### <a name="linux"></a>Linux

1. 导航到包含证书生成脚本和根 CA 证书的工作目录。

2. 为下游设备创建两个证书（主要和辅助）。 要使用的简易命名约定是依次使用 IoT 设备的名称以及主要标签或辅助标签创建证书。 例如：

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   此脚本命令将创建多个证书和密钥文件。 以下证书和密钥对需要复制到下游 IoT 设备，并在连接到 IoT 中心的应用程序中引用：

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. 从每个证书中检索 SHA1 指纹（在 IoT 中心上下文中称为“指纹”）。 指纹是由 40 个十六进制字符组成的字符串。 使用以下 openssl 命令查看证书并查找指纹：

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   使用自签名的 X.509 证书注册新 IoT 设备时，请提供主要指纹和辅助指纹。

### <a name="ca-signed-certificates"></a>CA 签名的证书

使用自签名证书对 IoT 设备进行身份验证时，需将解决方案的根 CA 证书上传到 IoT 中心。
然后执行验证，以向 IoT 中心证明你拥有该根 CA 证书。
最后，使用同一个根 CA 证书创建要放在 IoT 设备上的设备证书，使设备能够在 IoT 中心进行身份验证。

本部分中的证书用于[在 Azure IoT 中心设置 X.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)中的步骤。

#### <a name="windows"></a>Windows

1. 将根 CA 证书文件从工作目录 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 上传到 IoT 中心。

2. 使用 Azure 门户中提供的代码验证你是否拥有该根 CA 证书。

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. 创建下游设备的证书链。 使用 IoT 中心内用于注册设备的设备 ID。

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   此脚本命令将创建多个证书和密钥文件。 以下证书和密钥对需要复制到下游 IoT 设备，并在连接到 IoT 中心的应用程序中引用：

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. 将根 CA 证书文件从工作目录 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 上传到 IoT 中心。

2. 使用 Azure 门户中提供的代码验证你是否拥有该根 CA 证书。

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. 创建下游设备的证书链。 使用 IoT 中心内用于注册设备的设备 ID。

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   此脚本命令将创建多个证书和密钥文件。 以下证书和密钥对需要复制到下游 IoT 设备，并在连接到 IoT 中心的应用程序中引用：

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
