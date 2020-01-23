---
title: 创建测试证书-Azure IoT Edge |Microsoft Docs
description: 创建测试证书，并了解如何将其安装到 Azure IoT Edge 设备上，以便准备生产部署。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6f64a6714b9d795a1e809c555394be6f7671c63
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510662"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>创建用于测试 IoT Edge 设备功能的演示证书

IoT Edge 设备需要在运行时、模块和任何下游设备之间进行安全通信的证书。
如果没有证书颁发机构来创建所需的证书，可以使用演示证书在测试环境中试用 IoT Edge 功能。
本文介绍 IoT Edge 提供用于测试的证书生成脚本功能。

这些证书将在30天后过期，不应在任何生产方案中使用。

你可以在任何计算机上创建证书，然后将其复制到 IoT Edge 设备上。
使用主计算机创建证书比在 IoT Edge 设备本身生成证书更容易。
通过使用主计算机，你可以设置脚本一次，然后重复该过程以创建多个设备的证书。

## <a name="prerequisites"></a>必备组件

安装了 Git 的开发计算机。

## <a name="set-up-scripts"></a>设置脚本

GitHub 上的 IoT Edge 存储库包括可用于创建演示证书的证书生成脚本。
本部分说明了如何在 Windows 或 Linux 上准备要在计算机上运行的脚本。
如果使用的是 Linux 计算机，请直接跳到[linux 上](#set-up-on-linux)的安装。

### <a name="set-up-on-windows"></a>在 Windows 上设置

若要在 Windows 设备上创建演示证书，需要安装 OpenSSL，然后克隆生成脚本，并将其设置为在 PowerShell 中本地运行。

#### <a name="install-openssl"></a>安装 OpenSSL

在用于生成证书的计算机上安装 OpenSSL for Windows。
如果 Windows 设备上已安装 OpenSSL，则可以跳过此步骤，但请确保 OpenSSL 在 PATH 环境变量中可用。

可以通过多种方式来安装 OpenSSL，包括以下选项：

* **更简单：** 下载并安装所有[第三方 OpenSSL 二进制文件](https://wiki.openssl.org/index.php/Binaries)，例如，从[SourceForge 上的 OpenSSL](https://sourceforge.net/projects/openssl/)。 将 openssl.exe 的完整路径添加到 PATH 环境变量。

* **建议方法：** 在计算机上下载 OpenSSL 源代码并自行生成二进制文件，或者通过 [vcpkg](https://github.com/Microsoft/vcpkg) 生成。 下面列出的说明使用 vcpkg 下载源代码，并在 Windows 计算机上编译和安装 OpenSSL，所用的步骤都很简单。

   1. 导航到要安装 vcpkg 的目录。 按照说明下载并安装 [vcpkg](https://github.com/Microsoft/vcpkg)。

   2. 安装 vcpkg 后，从 powershell 提示符运行以下命令，以安装适用于 Windows x64 的 OpenSSL 包。 安装通常需要5分钟才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. 将 `<vcpkg path>\installed\x64-windows\tools\openssl` 添加到 PATH 环境变量，以便可以调用 openssl.exe 文件。

#### <a name="prepare-scripts-in-powershell"></a>在 PowerShell 中准备脚本

Azure IoT Edge git 存储库包含可用于生成测试证书的脚本。
在本部分中，将克隆 IoT Edge 存储库，并执行脚本。

1. 在管理员模式下打开 PowerShell 窗口。

2. 克隆 IoT Edge 的 git 存储库，其中包含用于生成演示证书的脚本。 使用 `git clone` 命令或[下载 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 导航到要在其中工作的目录。 在本文中，我们将 *\<WRKDIR >* 中调用此目录。 将在此工作目录中创建所有证书和密钥。

4. 将复制的存储库中的配置和脚本文件复制到工作目录。

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果将存储库下载为 ZIP，则文件夹名称为 `iotedge-master`，其余路径相同。

5. 启用 PowerShell 以运行脚本。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. 将脚本使用的函数引入 PowerShell 的全局命名空间。

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 窗口将显示一条警告，指出此脚本生成的证书仅用于测试目的，不应在生产方案中使用。

7. 验证是否已正确安装 OpenSSL，并确保现有证书不存在名称冲突。 如果出现问题，脚本输出应介绍如何在系统上修复这些问题。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>在 Linux 上设置

若要在 Windows 设备上创建演示证书，需要克隆生成脚本，并将其设置为在 bash 本地运行。

1. 克隆 IoT Edge 的 git 存储库，其中包含用于生成演示证书的脚本。

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 导航到要在其中工作的目录。 我们将在本文中将此目录称为 *\<WRKDIR >* 。 将在此目录中创建所有证书和密钥文件。
  
3. 将克隆的 IoT Edge 存储库中的配置和脚本文件复制到工作目录。

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

根 CA 证书用于创建所有其他演示证书来测试 IoT Edge 方案。
你可以继续使用相同的根 CA 证书，为多个 IoT Edge 或下游设备制作演示证书。

如果工作文件夹中已经有一个根 CA 证书，请不要创建新证书。
新的根 CA 证书将覆盖旧证书，从旧证书进行的任何下游证书都将停止工作。
如果需要多个根 CA 证书，请确保在单独的文件夹中对其进行管理。

在继续执行本部分中的步骤之前，请执行 "[设置脚本](#set-up-scripts)" 一节中的步骤，使用演示证书生成脚本准备工作目录。

### <a name="windows"></a>Windows

1. 导航到在其中放置证书生成脚本的工作目录。

1. 创建根 CA 证书并使其签名一个中间证书。 所有证书都放置在工作目录中。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此脚本命令会创建多个证书和密钥文件，但当文章请求**根 CA 证书**时，请使用以下文件：

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. 导航到在其中放置证书生成脚本的工作目录。

1. 创建根 CA 证书和一个中间证书。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   此脚本命令会创建多个证书和密钥文件，但当文章请求**根 CA 证书**时，请使用以下文件：

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>创建 IoT Edge 设备 CA 证书

转到生产环境的每个 IoT Edge 设备都需要一个从 yaml 文件引用的设备 CA 证书。 设备 CA 证书负责为设备上运行的模块创建证书。 这也是 IoT Edge 设备连接到下游设备时验证其标识的方式。

在继续执行本部分中的步骤之前，请执行[设置脚本](#set-up-scripts)和[创建根 CA 证书](#create-root-ca-certificate)部分中的步骤。

### <a name="windows"></a>Windows

1. 导航到具有证书生成脚本和根 CA 证书的工作目录。

2. 在以下命令中创建 IoT Edge 设备 CA 证书和私钥。 提供 CA 证书的名称（例如**MyEdgeDeviceCA**），用于对输出文件进行命名。

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   此脚本命令创建多个证书和密钥文件。 以下证书和密钥对需要复制到 IoT Edge 设备上，并在 yaml 文件中引用：

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

传入这些脚本的网关设备名称不应与 yaml 中的 "hostname" 参数相同。 脚本通过将 "ca" 字符串附加到网关设备名称来防止名称冲突，以防用户在这两个位置使用同一名称进行 IoT Edge 设置时出现问题。 但最好避免使用相同的名称。

### <a name="linux"></a>Linux

1. 导航到具有证书生成脚本和根 CA 证书的工作目录。

2. 在以下命令中创建 IoT Edge 设备 CA 证书和私钥。 提供 CA 证书的名称（例如**MyEdgeDeviceCA**），用于对输出文件进行命名。

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   此脚本命令创建多个证书和密钥文件。 以下证书和密钥对需要复制到 IoT Edge 设备上，并在 yaml 文件中引用：

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

传入这些脚本的网关设备名称不应与 yaml 中的 "hostname" 参数相同。 脚本通过将 "ca" 字符串附加到网关设备名称来防止名称冲突，以防用户在这两个位置使用同一名称进行 IoT Edge 设置时出现问题。 但最好避免使用相同的名称。

## <a name="create-x509-certs-for-downstream-devices"></a>为下游设备创建 x.509 证书

如果要为网关方案设置下游 IoT 设备，可以为 x.509 authentication 生成演示证书。
可以通过两种方式使用 x.509 证书对 IoT 设备进行身份验证：使用自签名证书或使用证书颁发机构（CA）签名证书。
对于 x.509 自签名身份验证，有时称为指纹身份验证，则需要创建新的证书以放置在 IoT 设备上。
这些证书在其中有一个指纹，可与 IoT 中心共享以便进行身份验证。
对于 x.509 证书颁发机构（CA）签名的身份验证，需要一个在 IoT 中心注册的根 CA 证书，用于为 IoT 设备签署证书。
允许使用由根 CA 证书或其任何中间证书颁发的证书的任何设备进行身份验证。

证书生成脚本可帮助你创建演示证书来测试其中的任何一种身份验证方案。

在继续执行本部分中的步骤之前，请执行[设置脚本](#set-up-scripts)和[创建根 CA 证书](#create-root-ca-certificate)部分中的步骤。

### <a name="self-signed-certificates"></a>自签名证书

使用自签名证书对 IoT 设备进行身份验证时，需要根据解决方案的根 CA 证书创建设备证书。
然后，从证书中检索要提供给 IoT 中心的十六进制 "指纹"。
IoT 设备还需要其设备证书的副本，以便可以使用 IoT 中心进行身份验证。

#### <a name="windows"></a>Windows

1. 导航到具有证书生成脚本和根 CA 证书的工作目录。

2. 为下游设备创建两个证书（主证书和辅助证书）。 使用简单的命名约定是使用 IoT 设备的名称以及主标签或辅助标签创建证书。 例如：

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   此脚本命令创建多个证书和密钥文件。 需要将以下证书和密钥对复制到下游 IoT 设备，并在连接到 IoT 中心的应用程序中引用这些证书和密钥对：

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. 从每个证书检索 SHA1 指纹（在 IoT 中心上下文中称为指纹）。 指纹是40的十六进制字符串。 使用以下 openssl 命令查看证书并找到指纹：

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   运行此命令两次，一次用于主证书，一次用于辅助证书。 使用自签名 x.509 证书注册新的 IoT 设备时，为这两个证书提供指纹。

#### <a name="linux"></a>Linux

1. 导航到具有证书生成脚本和根 CA 证书的工作目录。

2. 为下游设备创建两个证书（主证书和辅助证书）。 使用简单的命名约定是使用 IoT 设备的名称以及主标签或辅助标签创建证书。 例如：

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   此脚本命令创建多个证书和密钥文件。 需要将以下证书和密钥对复制到下游 IoT 设备，并在连接到 IoT 中心的应用程序中引用这些证书和密钥对：

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. 从每个证书检索 SHA1 指纹（在 IoT 中心上下文中称为指纹）。 指纹是40的十六进制字符串。 使用以下 openssl 命令查看证书并找到指纹：

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   使用自签名 x.509 证书注册新的 IoT 设备时，需要提供主要和次要指纹。

### <a name="ca-signed-certificates"></a>CA 签名的证书

使用自签名证书对 IoT 设备进行身份验证时，需要将解决方案的根 CA 证书上载到 IoT 中心。
然后，执行验证来证明你拥有根 CA 证书的 IoT 中心。
最后，使用相同的根 CA 证书创建要置于 IoT 设备上的设备证书，以便可以使用 IoT 中心进行身份验证。

本部分中的证书适用于在[Azure IoT 中心设置 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)中的步骤。

#### <a name="windows"></a>Windows

1. 将根 CA 证书文件从工作目录上传 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`到 IoT 中心。

2. 使用 Azure 门户中提供的代码验证是否拥有该根 CA 证书。

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. 为下游设备创建证书链。 使用在 IoT 中心注册设备时使用的设备 ID。

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   此脚本命令创建多个证书和密钥文件。 需要将以下证书和密钥对复制到下游 IoT 设备，并在连接到 IoT 中心的应用程序中引用这些证书和密钥对：

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. 将根 CA 证书文件从工作目录上传 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`到 IoT 中心。

2. 使用 Azure 门户中提供的代码验证是否拥有该根 CA 证书。

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. 为下游设备创建证书链。 使用在 IoT 中心注册设备时使用的设备 ID。

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   此脚本命令创建多个证书和密钥文件。 需要将以下证书和密钥对复制到下游 IoT 设备，并在连接到 IoT 中心的应用程序中引用这些证书和密钥对：

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
