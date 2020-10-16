---
title: 安装 Azure IoT Edge |Microsoft Docs
description: 在 Windows 或 Linux 设备上 Azure IoT Edge 安装说明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: kgremban
ms.openlocfilehash: 7ab62b04f8bea76c7efb587665f87ccaf123da24
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108994"
---
# <a name="install-or-uninstall-the-azure-iot-edge-runtime"></a>安装或卸载 Azure IoT Edge 运行时

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

设置 IoT Edge 设备有两个步骤。 第一步是安装本文涵盖的运行时及其依赖项。 第二步是将设备连接到云中的标识，并设置 IoT 中心的身份验证。 后续文章中介绍了这些步骤。

本文列出了在 Linux 或 Windows 设备上安装 Azure IoT Edge 运行时的步骤。 对于 Windows 设备，你可以使用 Linux 容器或 Windows 容器进行其他选择。 目前，Windows 容器建议用于生产方案。 Windows 上的 Linux 容器可用于开发和测试方案，尤其是在 Windows 电脑上进行开发以便部署到 Linux 设备时。

## <a name="prerequisites"></a>必备条件

有关生产方案当前支持的操作系统的最新信息，请参阅 [Azure IoT Edge 支持的系统](support.md#operating-systems)

# <a name="linux"></a>[Linux](#tab/linux)

具有 X64、ARM32 或 ARM64 Linux 设备。 Microsoft 提供适用于 Ubuntu Server 16.04、Ubuntu Server 18.04 和 Raspbian Stretch 操作系统的安装包。

>[!NOTE]
>ARM64 设备支持 [公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

准备你的设备以访问 Microsoft 安装包。

1. 安装与设备操作系统匹配的存储库配置。

   * **Ubuntu Server 16.04**：

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ubuntu Server 18.04**：

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspbian Stretch**：

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. 将生成的列表复制到源. d 目录。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. 安装 Microsoft GPG 公钥。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

# <a name="windows"></a>[Windows](#tab/windows)

### <a name="windows-version"></a>Windows 版本

Windows 容器 IoT Edge 需要 Windows 版本 1809/版本17762，这是最新的 [windows 长期支持构建](/windows/release-information/)。 对于开发和测试方案，支持容器功能的任何 SKU (Pro、Enterprise、Server 等 ) 均适用。 但是，在转到生产环境之前，请务必查看受支持的系统列表。

与 Linux 容器 IoT Edge 可以在满足 [Docker 桌面要求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的任何 Windows 版本上运行。

### <a name="container-engine-requirements"></a>容器引擎要求

Azure IoT Edge 依赖于 [与 OCI 兼容](https://www.opencontainers.org/) 的容器引擎。 确保你的设备可以支持容器。

如果要在虚拟机上安装 IoT Edge，请启用嵌套虚拟化并分配至少 2 GB 的内存。 就 Hyper-V 来说，第 2 代虚拟机已默认启用嵌套虚拟化。 如果使用 VMware，则可通过切换开关在虚拟机上启用此功能。

如果要在 IoT Core 设备上安装 IoT Edge，请在 [远程 PowerShell 会话](/windows/iot-core/connect-your-device/powershell) 中使用以下命令来检查 Windows 容器在你的设备上是否受支持：

```powershell
Get-Service vmcompute
```

---

Azure IoT Edge 软件包受每个包 (`usr/share/doc/{package-name}` 或目录) 的许可条款的约束 `LICENSE` 。 使用包之前，请阅读许可条款。 安装和使用包即表示你接受这些条款。 如果不同意许可条款，请不要使用该软件包。

## <a name="install-a-container-engine"></a>安装容器引擎

Azure IoT Edge 依赖于 OCI 兼容的容器运行时。 对于生产方案，建议使用基于小鲸鱼的引擎。 Moby 引擎是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与 Moby 运行时兼容。

# <a name="linux"></a>[Linux](#tab/linux)

更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

安装 Moby 引擎。

   ```bash
   sudo apt-get install moby-engine
   ```

如果在安装小鲸鱼容器引擎时出现错误，请验证 Linux 内核的小鲸鱼兼容性。 某些嵌入式设备制造商提供了包含自定义 Linux 内核的设备映像，但没有容器引擎兼容性所需的功能。 运行以下命令，该命令使用由小鲸鱼提供的 [检查-config 脚本](https://github.com/moby/moby/blob/master/contrib/check-config.sh) 来检查内核配置：

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

在该脚本的输出中，检查 `Generally Necessary` 是否启用了和下的所有项目 `Network Drivers` 。 如果缺少功能，请通过从源重建内核并选择要包含在相应的内核中的关联模块来启用它们。同样，如果使用或等内核配置生成器 `defconfig` `menuconfig` ，请查找并启用相应的功能，并相应地重建内核。 部署新修改的内核以后，请再次运行 check-config 脚本，验证是否已成功启用所有必需功能。

# <a name="windows"></a>[Windows](#tab/windows)

对于生产方案，请使用安装脚本中包含的基于小鲸鱼的引擎。 无其他步骤来安装引擎。

对于 Linux 容器 IoT Edge，需要提供自己的容器运行时。 在设备上安装 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) ，并将其配置为 [使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) ，然后再继续。

---

## <a name="install-the-iot-edge-security-daemon"></a>安装 IoT Edge 安全守护程序

IoT Edge 安全守护程序提供和维护 IoT Edge 设备上的安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 运行时的其余部分来启动设备。

本节中的步骤表示在具有 internet 连接的设备上安装最新版本的典型过程。 如果需要安装特定版本，如预发布版本，或在脱机状态下安装，请按照下一节中的 [脱机或特定版本安装](#offline-or-specific-version-installation) 步骤进行操作。

# <a name="linux"></a>[Linux](#tab/linux)

更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

查看可用的 IoT Edge 版本。

   ```bash
   apt list -a iotedge
   ```

如果要安装最新版本的安全守护程序，请使用以下命令，该命令还会安装最新版本的 **libiothsm** 包：

   ```bash
   sudo apt-get install iotedge
   ```

如果要安装特定版本的安全守护程序，请从 apt 列表输出中指定该版本。 还要为 libiothsm-std 包指定同一版本，否则将安装其最新版本。 例如，以下命令将安装最新版本 1.0.8 版本：

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

如果未列出你要安装的版本，请按照下一节中的 [脱机或特定版本安装](#offline-or-specific-version-installation) 步骤进行操作。 此部分说明了如何将 IoT Edge 安全守护程序的任何之前版本或候选发布版本作为目标。

# <a name="windows"></a>[Windows](#tab/windows)

>[!TIP]
>对于 IoT Core 设备，建议使用远程 PowerShell 会话运行安装命令。 有关详细信息，请参阅[将 PowerShell 用于 Windows IoT](/windows/iot-core/connect-your-device/powershell)。

1. 以管理员身份运行 PowerShell。

   使用 PowerShell 的 AMD64 会话，而不是 PowerShell (x86) 。 如果不确定要使用的会话类型，请运行以下命令：

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. 运行 [IoTEdge](reference-windows-scripts.md#deploy-iotedge) 命令，该命令执行以下任务：

   * 检查 Windows 计算机是否在受支持的版本中。
   * 启用容器功能。
   * 下载小鲸鱼引擎和 IoT Edge 运行时。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge`命令默认为使用 Windows 容器。 如果要使用 Linux 容器，请添加 `ContainerOs` 参数：

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. 此时，IoT Core 设备可能会自动重启。 Windows 10 或 Windows Server 设备可能会提示您重新启动。 如果是这样，请立即重启设备。

在设备上安装 IoT Edge 时，可以使用其他参数来修改该过程，包括：

* 定向流量，使其通过代理服务器
* 将安装程序指向本地目录以进行脱机安装。

有关这些其他参数的详细信息，请参阅 [适用于 Windows 上的 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

---

由于容器引擎和 IoT Edge 运行时已安装在你的设备上，你可以开始下一步，即向 IoT 中心注册你的设备，并将设备设置为其云标识和身份验证信息。

根据要使用的身份验证类型，选择以下文章：

* [对称密钥身份验证](how-to-manual-provision-symmetric-key.md) 的速度更快。
* [X.509 证书身份验证](how-to-manual-provision-x509.md) 更安全，适用于生产方案。

## <a name="offline-or-specific-version-installation"></a>脱机或特定版本安装

本部分中的步骤适用于标准安装步骤未涵盖的方案。 这可能包括：

* 脱机安装 IoT Edge
* 安装候选发布版本
* 在 Windows 上，安装最新版本以外的版本

# <a name="linux"></a>[Linux](#tab/linux)

如果想要安装特定版本的无法通过 `apt-get install` 获取的 Azure IoT Edge 运行时，请使用本部分中的步骤。 Microsoft 包列表仅包含有限的一组最新版本及其子版本，因此，这些步骤适用于想要安装较旧版本或候选发布版本的任何用户。

使用 curl 命令，可以直接从 IoT Edge GitHub 存储库将组件文件作为目标。

<!-- TODO: Offline installation? -->

1. 导航到 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)，找到需要将其作为目标的发行版。

2. 展开该版本的“资产”部分。

3. 每个版本应该都有用于 IoT Edge 安全守护程序和 hsmlib 的新文件。 使用以下命令更新这些组件。

   1. 找到与 IoT Edge 设备的体系结构匹配的 **libiothsm-std** 文件。 右键单击文件链接并复制链接地址。

   2. 将复制的链接用在以下命令中，安装该版本的 hsmlib：

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. 找到与 IoT Edge 设备的体系结构匹配的 **iotedge** 文件。 右键单击文件链接并复制链接地址。

   4. 将复制的链接用在以下命令中，安装该版本的 IoT Edge 安全守护程序：

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

# <a name="windows"></a>[Windows](#tab/windows)

在安装过程中，会下载三个文件：

* 一个 PowerShell 脚本，其中包含安装说明
* Microsoft Azure IoT Edge cab，其中包含 IoT Edge 安全守护程序 (iotedged)、Moby 容器引擎和 Moby CLI
* Visual C++ Redistributable Package（VC 运行时）安装程序

如果在安装过程中设备会脱机，或者要安装 IoT Edge 的特定版本，则可提前将这些文件下载到设备。 当安装时，请将安装脚本指向包含已下载文件的目录。 安装程序首先会检查该目录，然后仅下载未找到的组件。 如果所有文件都可脱机使用，则无需连接到 Internet 连接即可安装。

1. 有关最新的 IoT Edge 安装文件以及旧版本，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

2. 找到要安装的版本，然后从发行说明的“资产”部分将以下文件下载到 IoT 设备上：

   * IoTEdgeSecurityDaemon.ps1
   * 1\.0.9 或更高版本中的 Microsoft-Azure-IoTEdge-amd64.cab，或者 1.0.8 或更低版本中的 Microsoft-Azure-IoTEdge.cab。

   从 1.0.9 开始，也可以使用 Microsoft-Azure-IotEdge-arm32.cab（仅用于测试目的）。 Windows ARM32 设备目前不支持 IoT Edge。

   请务必使用与所用 .cab 文件版本相同的 PowerShell 脚本，因为功能会进行更改以支持每个版本中的特性。

3. 如果下载的 .cab 文件在其上有体系结构后缀，则只需将该文件重命名为“Microsoft-Azure-IoTEdge.cab”即可  。

4. （可选）下载 Visual C++ Redistributable 的安装程序。 例如，PowerShell 脚本使用此版本：[vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe)。 将安装程序保存到 IoT 设备上 IoT Edge 文件所在的文件夹中。

5. 若要使用脱机组件进行安装，请[使用点获取 PowerShell 脚本本地副本的来源](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)。 

6. 运行带有参数的 [IoTEdge](reference-windows-scripts.md#deploy-iotedge) 命令 `-OfflineInstallationPath` 。 提供文件目录的绝对路径。 例如，

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   部署命令会使用已提供的本地文件目录中的任何组件。 如果缺少 .cab 文件或 Visual C++ 安装程序，部署命令会尝试下载该文件或安装程序。

---

现在容器引擎和 IoT Edge 运行时已安装到设备上，接下来可以开始执行下一步，即 [在 IoT 中心对 IoT Edge 设备进行身份验证](how-to-manual-provision-symmetric-key.md)。

## <a name="uninstall-iot-edge"></a>卸载 IoT Edge

如果要从设备中删除 IoT Edge 安装，请使用以下命令。

# <a name="linux"></a>[Linux](#tab/linux)

删除 IoT Edge 运行时。

```bash
sudo apt-get remove --purge iotedge
```

删除 IoT Edge 运行时后，创建的所有容器都将停止，但仍存在于设备上。 查看所有容器以了解哪些容器仍然存在。

```bash
sudo docker ps -a
```

从设备中删除容器，包括两个运行时容器。

```bash
sudo docker rm -f <container name>
```

最后，从设备中删除容器运行时。

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

# <a name="windows"></a>[Windows](#tab/windows)

如果要从 Windows 设备中删除 IoT Edge 安装，请使用管理 PowerShell 窗口中的 [IoTEdge](reference-windows-scripts.md#uninstall-iotedge) 命令。 此命令会删除 IoT Edge 运行时，以及现有的配置和 Moby 引擎数据。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

此 `Uninstall-IoTEdge` 命令在 Windows IoT Core 上不起作用。 若要删除 IoT Edge，需要重新部署 Windows IoT Core 映像。

有关卸载选项的详细信息，请使用命令 `Get-Help Uninstall-IoTEdge -full`。

---

## <a name="next-steps"></a>后续步骤

安装 IoT Edge 运行时后，请将设备配置为连接到 IoT 中心。 以下文章介绍了如何在云中注册新设备，然后向设备提供其标识和身份验证信息。

根据要使用的身份验证类型，选择下一篇文章：

* **对称密钥**： IoT 中心和 IoT Edge 设备都具有安全密钥的副本。 当设备连接到 IoT 中心时，它们会检查密钥是否匹配。 此身份验证方法的启动速度更快，但并不安全。

  [设置 Azure IoT Edge 设备进行对称密钥身份验证](how-to-manual-provision-symmetric-key.md)

* **X.509 自签名**： IoT Edge 设备具有 x.509 标识证书，并且为 IoT 中心提供证书的指纹。 当设备连接到 IoT 中心时，它们会将证书与其指纹进行比较。 此身份验证方法更加安全，建议用于生产方案。

  [使用 x.509 证书身份验证设置 Azure IoT Edge 设备](how-to-manual-provision-x509.md)