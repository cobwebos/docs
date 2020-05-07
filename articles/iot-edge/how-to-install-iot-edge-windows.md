---
title: 在 Windows 上安装 Azure IoT Edge | Microsoft Docs
description: 有关在 Windows 10、Windows Server 和 Windows IoT Core 上安装 Azure IoT Edge 的说明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: e95f68610f8469a829255d6a16115dcf728ef612
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856749"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>在 Windows 上安装 Azure IoT Edge 运行时

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。

若要详细了解 IoT Edge 运行时，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了使用 Windows 容器在 Windows x64 (AMD/Intel) 系统上安装 Azure IoT Edge 运行时的步骤。

> [!NOTE]
> 当 IoT Edge 模块（进程隔离的 Windows Nano Server 容器）正在运行时，一个已知的 Windows 操作系统问题会阻止转换到睡眠和休眠电源状态。 此问题会影响设备的电池寿命。
>
> 作为解决方法，在使用这些电源状态之前，请使用 `Stop-Service iotedge` 命令停止任何正在运行的 IoT Edge 模块。

不推荐或支持在 Windows 系统上使用 Linux 容器作为 Azure IoT Edge 的生产配置。 但可将其用于开发和测试。 有关详细信息，请参阅[使用 Windows 上的 IoT Edge 运行 Linux 容器](how-to-install-iot-edge-windows-with-linux.md)。

有关最新 IoT Edge 版本包含的功能的信息，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="prerequisites"></a>先决条件

参考本部分检查你的 Windows 设备是否支持 IoT Edge，并在安装之前为容器引擎准备好该设备。

### <a name="supported-windows-versions"></a>支持的 Windows 版本

用于 Windows 的 IoT Edge 需要 Windows 版本 1809/内部版本 17763，这是最新的 [Windows 长期支持版本](https://docs.microsoft.com/windows/release-information/)。 对于 Windows SKU 支持，请根据你是准备生产方案还是开发和测试方案，查看支持的内容：

* **生产**：有关生产方案目前支持哪些操作系统的最新信息，请参阅 [Azure IoT Edge 支持的系统](support.md#operating-systems)。
* **开发和测试**：对于开发和测试方案，带有 Windows 容器的 Azure IoT Edge 可以安装在支持容器功能的任何版本的 Windows 10 或 Windows Server 2019 上。

IoT Core 设备必须包含 IoT Core Windows 容器可选功能才能支持 IoT Edge 运行时。 在[远程 PowerShell 会话](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)中使用以下命令来检查设备是否支持 Windows 容器：

```powershell
Get-Service vmcompute
```

如果存在该服务，则应该会收到成功的响应，其中服务状态列为“正在运行”  。 如果找不到 `vmcompute` 服务，则设备不满足 IoT Edge 的要求。 请与硬件提供商联系，询问此功能是否支持。

### <a name="prepare-for-a-container-engine"></a>为容器引擎做好准备

Azure IoT Edge 依赖于 [OCI 兼容的](https://www.opencontainers.org/)容器引擎。 对于生产方案，请使用安装脚本中包含的 Moby 引擎在 Windows 设备上运行 Windows 容器。

## <a name="install-iot-edge-on-a-new-device"></a>在新设备上安装 IoT Edge

>[!NOTE]
>Azure IoT Edge 软件程序包受制于程序包中的许可条款（位于 LICENSE 目录中）。 使用程序包之前请阅读这些许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用程序包。

某个 PowerShell 脚本将下载并安装 Azure IoT Edge 安全守护程序。 然后，安全守护程序将启动两个运行时模块中的第一个，即 IoT Edge 代理，以便能够远程部署其他模块。

>[!TIP]
>对于 IoT Core 设备，我们建议使用 RemotePowerShell 会话运行安装命令。 有关详细信息，请参阅[将 PowerShell 用于 Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)。

首次在设备上安装 IoT Edge 运行时时，需要使用 IoT 中心内的标识预配该设备。 可以使用 IoT 中心提供的设备连接字符串手动预配单个 IoT Edge 设备。 或者，可以使用设备预配服务 (DPS) 自动预配设备，需要设置多个设备时，这种做法非常有用。 根据预配选项，选择合适的安装脚本。

以下部分介绍新设备上 IoT Edge 安装脚本的常见用例和参数。

### <a name="option-1-install-and-manually-provision"></a>选项 1：安装和手动预配

在这第一个选项中，提供 IoT 中心生成的、用于预配设备的**设备连接字符串**。

此示例演示使用 Windows 容器进行的手动安装：

1. 注册新的 IoT Edge 设备并检索**设备连接字符串**（如果尚未这样做）。 复制连接字符串，以便稍后在本部分中使用。 可以使用以下工具完成此步骤：

   * [Azure 门户](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. 以管理员身份运行 PowerShell。

   >[!NOTE]
   >使用 PowerShell 的 AMD64 会话安装 IoT Edge，不要使用 PowerShell (x86)。 如果不确定您使用的是什么会话类型，请运行以下命令：
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **Deploy-IoTEdge** 命令检查 Windows 计算机是否使用了支持的版本，启用容器功能，然后下载 moby 运行时和 IoT Edge 运行时。 该命令默认使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. 此时，IoT Core 设备可能会自动重启。 其他 Windows 10 或 Windows Server 设备可能会提示你重启。 如果是这样，请立即重启设备。 设备准备就绪后，再次以管理员身份运行 PowerShell。

5. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时  。 该命令默认为使用 Windows 容器手动预配。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. 出现提示时，请提供在步骤 1 中检索到的设备连接字符串。 该设备连接字符串会将物理设备与 IoT 中心内的设备 ID 相关联。

   该设备连接字符串采用以下格式（不包括引号）：`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. 使用[验证安装是否成功](#verify-successful-installation)中的步骤检查设备上的 IoT Edge 状态。

手动安装和预配设备时，可以使用附加的参数来修改安装，包括：

* 定向流量，使其通过代理服务器
* 将安装程序指向某个脱机目录
* 声明特定的代理容器映像，并提供凭据（如果该映像位于专用注册表中）

有关这些安装选项的详细信息，请直接跳到[所有安装参数](#all-installation-parameters)。

### <a name="option-2-install-and-automatically-provision"></a>选项 2：安装和自动预配

在这第二个选项中，使用 IoT 中心设备预配服务来预配设备。 提供设备预配服务实例的**范围 ID** 以及特定于首选[证明机制](../iot-dps/concepts-security.md#attestation-mechanism)的任何其他信息：

* [在 Windows 上使用虚拟 TPM 创建和预配模拟 IoT Edge 设备](how-to-auto-provision-simulated-device-windows.md)
* [使用 X.509 证书创建和预配模拟 IoT Edge 设备](how-to-auto-provision-x509-certs.md)
* [使用对称密钥证明创建和预配 IoT Edge 设备](how-to-auto-provision-symmetric-keys.md)

自动安装和预配设备时，可以使用附加的参数来修改安装，包括：

* 定向流量，使其通过代理服务器
* 将安装程序指向某个脱机目录
* 声明特定的代理容器映像，并提供凭据（如果该映像位于专用注册表中）

有关这些安装选项的详细信息，请继续阅读本文，或转到[所有安装参数](#all-installation-parameters)。

## <a name="offline-or-specific-version-installation"></a>脱机或特定版本安装

安装期间，下载三个文件：

* 一个 PowerShell 脚本，其中包含安装说明
* Microsoft Azure IoT Edge cab，其中包含 IoT Edge 安全守护程序（iotedged）、小鲸鱼容器引擎和小鲸鱼 CLI
* Visual C++ 可再发行组件包（VC 运行时）安装程序

如果在安装过程中设备将处于脱机状态，或者如果要安装 IoT Edge 的特定版本，则可以提前将这些文件下载到设备。 当安装时，请将安装脚本指向包含已下载文件的目录。 安装程序首先检查该目录，然后仅下载未找到的组件。 如果所有文件都可脱机使用，则无需连接到 Internet 连接即可安装。

你还可以使用脱机安装路径参数来更新 IoT Edge。 有关详细信息，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。

1. 有关最新的 IoT Edge 安装文件以及旧版本，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

2. 找到要安装的版本，并从发行说明的 "**资产**" 部分将以下文件下载到 IoT 设备：

   * IoTEdgeSecurityDaemon
   * Microsoft-Azure-IoTEdge-amd64 发布1.0.9 或更高版本，或 Microsoft-Azure-IoTEdge 版本1.0.8 及更早版本。

   Microsoft-Azure-IotEdge-arm32 仅在1.0.9 中提供，以供测试之用。 Windows ARM32 设备目前不支持 IoT Edge。

   使用与你使用的 .cab 文件相同的版本中的 PowerShell 脚本非常重要，因为此功能将更改为支持每个版本中的功能。

3. 如果下载的 .cab 文件在其上有体系结构后缀，请将该文件重命名为**Microsoft-Azure-IoTEdge**。

4. 也可以下载 Visual C++ 可再发行组件的安装程序。 例如，PowerShell 脚本使用以下版本： [vc_redist](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe)。 将安装程序保存在 IoT 设备上与 IoT Edge 文件相同的文件夹中。

5. 若要安装具有脱机组件的点，请在 PowerShell 脚本的本地副本上保存[点](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing)。 然后，在`Deploy-IoTEdge`命令`-OfflineInstallationPath`中使用参数，并提供文件目录的绝对路径。 例如，应用于对象的

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   部署命令将使用所提供的本地文件目录中的任何组件。 如果缺少 .cab 文件或 Visual C++ 安装程序，则它将尝试下载。

6. 运行`Initialize-IoTEdge`命令，在 IoT 中心内使用标识设置设备。 提供手动设置的设备连接字符串，或选择上一次[自动预配](#option-2-install-and-automatically-provision)部分中所述的方法之一。

   如果设备在运行`Deploy-IoTEdge`后重新启动，则在运行`Initialize-IoTEdge`之前，点将 PowerShell 脚本重新启动。

有关脱机安装选项的详细信息，请直接跳到了解[所有安装参数](#all-installation-parameters)。

## <a name="verify-successful-installation"></a>验证是否成功安装

检查 IoT Edge 服务的状态。 该服务应列为“正在运行”。  

```powershell
Get-Service iotedge
```

检查过去 5 分钟的服务日志。 如果刚完成了 IoT Edge 运行时的安装，则可能会在运行 **Deploy-IoTEdge** 和**Initialize-IoTEdge** 之间看到一系列的错误。 这些错误是预期的错误，因为服务在配置前尝试启动。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

运行[疑难解答工具](troubleshoot.md#run-the-check-command)以检查最常见的配置和网络错误。

```powershell
iotedge check
```

在将第一个模块部署到设备上的 IoT Edge 之前，**$edgeHub** 系统模块不会部署到设备。 因此，自动检查会返回一个针对 `Edge Hub can bind to ports on host` 连接性检查的错误。 除非在将模块部署到设备后发生此错误，否则可以忽略此错误。

最后，列出正在运行的模块：

```powershell
iotedge list
```

在全新安装之后，应该看到运行的唯一模块是 **edgeAgent**。 首次[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)后，其他系统模块 **edgeHub** 也会在设备上启动。

## <a name="manage-module-containers"></a>管理模块容器

IoT Edge 服务要求在设备上运行容器引擎。 将模块部署到设备时，IoT Edge 运行时将使用容器引擎从云中的注册表提取容器映像。 IoT Edge 服务允许与模块交互和检索日志，但有时，你可能想要使用容器引擎来与容器本身交互。

有关模块概念的详细信息，请参阅[了解 Azure IoT Edge 模块](iot-edge-modules.md)。

如果在 Windows IoT Edge 设备上运行 Windows 容器，则 IoT Edge 安装中已包含 Moby 容器引擎。 Moby 引擎所基于的标准与 Docker 相同，可在 Docker Desktop 所在的同一台计算机上同时运行。 因此，若要以 Moby 引擎管理的容器为目标，则必须专门将该引擎指定为目标，而不要以 Docker 为目标。

例如，若要列出所有 Docker 映像，可使用以下命令：

```powershell
docker images
```

若要列出所有 Moby 映像，可以使用指向 Moby 引擎的指针修改上述命令：

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

引擎 URI 将在安装脚本的输出中列出，也可以在 config.yaml 文件的容器运行时设置节中找到它。

![config.yaml 中的 moby_runtime uri](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

若要详细了解可以使用哪些命令来与设备上运行的容器和映像交互，请参阅 [Docker 命令行接口](https://docs.docker.com/engine/reference/commandline/docker/)。

## <a name="uninstall-iot-edge"></a>卸载 IoT Edge

若要从 Windows 设备中删除 IoT Edge 安装，请在 PowerShell 管理窗口中使用以下命令。 此命令会删除 IoT Edge 运行时，以及现有的配置和 Moby 引擎数据。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Uninstall-IoTEdge 命令在 Windows IoT Core 中无法运行。 若要从 Windows IoT Core 设备中删除 IoT Edge，需要重新部署 Windows IoT Core 映像。

有关卸载选项的详细信息，请使用命令 `Get-Help Uninstall-IoTEdge -full`。

## <a name="verify-installation-script"></a>验证安装脚本

本文中提供的安装命令使用 WebRequest cmdlet 来请求中`aka.ms/iotedge-win`的安装脚本。 此链接指向最近`IoTEdgeSecurityDaemon.ps1` [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)中的脚本。 你还可以从特定版本下载此脚本或脚本的版本，以便在 IoT Edge 设备上运行安装命令。

提供的脚本已签名以提高安全性。 可以通过将脚本下载到设备，然后运行以下 PowerShell 命令来验证签名：

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

如果验证签名，则输出状态**有效**。

## <a name="all-installation-parameters"></a>所有安装参数

前面几个部分介绍了常见安装方案，并举例说明了如何使用参数来修改安装脚本。 本部分提供用于安装、更新或卸载 IoT Edge 的通用参数的参考表格。

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Deploy-IoTEdge 命令下载并部署 IoT Edge 安全守护程序及其依赖项。 该部署命令接受这些通用参数，此外还接受其他一些参数。 有关完整列表，请使用命令 `Get-Help Deploy-IoTEdge -full`。  

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows**或**Linux** | 如果未指定容器操作系统，则 Windows 是默认值。<br><br>对于 Windows 容器，IoT Edge 使用安装中包含的 moby 容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 |
| **代理** | 代理 URL | 如果设备需要通过代理服务器来连接 Internet，请包含此参数。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目录路径 | 如果包含此参数，则安装程序将在列出的目录中检查安装时所需的 IoT Edge cab 和 VC 运行时 MSI 文件。 系统会下载该目录中不存在的任何文件。 如果这两个文件在该目录中存在，则无需建立 Internet 连接即可安装 IoT Edge。 还可以通过此参数来使用特定的版本。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **RestartIfNeeded** | 无 | 此标志可让部署脚本在无提示的情况下根据需要重启计算机。 |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge 命令使用设备连接字符串和操作详细信息配置 IoT Edge。 然后，此命令生成的许多信息将存储在 iotedge\config.yaml 文件中。 该初始化命令接受这些通用参数，此外还接受其他一些参数。 有关完整列表，请使用命令 `Get-Help Initialize-IoTEdge -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **手动** | 无 | **开关参数**。 如果未指定预配类型，则 manual 是默认值。<br><br>声明你要提供设备连接字符串来手动预配设备 |
| **分发** | 无 | **开关参数**。 如果未指定预配类型，则 manual 是默认值。<br><br>声明你要提供设备预配服务 (DPS) 范围 ID 和设备的注册 ID，以通过 DPS 进行预配。  |
| **DeviceConnectionString** | 已在 IoT 中心注册的 IoT Edge 设备中的连接字符串，括在单引号中 | **需要**手动预配。 如果未在脚本参数中提供连接字符串，系统将提示您输入一个连接字符串。 |
| **ScopeId** | 与 IoT 中心关联的设备预配服务实例中的范围 ID。 | DPS 预配**必需**的。 如果未在脚本参数中提供作用域 ID，系统将提示您输入一个。 |
| **RegistrationId** | 设备生成的注册 ID | 如果使用 TPM 或对称密钥证明，则**需要**使用 DPS 预配。 如果使用 x.509 证书证明，则为**可选**。 |
| **X509IdentityCertificate** | 设备上的 x.509 设备标识证书的 URI 路径。 | 如果使用 x.509 证书证明，则**需要**使用 DPS 预配。 |
| **X509IdentityPrivateKey** | 设备上的 x.509 设备标识证书密钥的 URI 路径。 | 如果使用 x.509 证书证明，则**需要**使用 DPS 预配。 |
| **SymmetricKey** | 使用 DPS 时用于预配 IoT Edge 设备标识的对称密钥 | 如果使用对称密钥证明，则**需要**使用 DPS 预配。 |
| **ContainerOs** | **Windows**或**Linux** | 如果未指定容器操作系统，则 Windows 是默认值。<br><br>对于 Windows 容器，IoT Edge 使用安装中包含的 moby 容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **AgentImage** | IoT Edge 代理映像 URI | 默认情况下，新的 IoT Edge 安装使用 IoT Edge 代理映像的最新滚动标记。 使用此参数可为映像版本设置特定的标记，或者提供自己的代理映像。 有关详细信息，请参阅[了解 IoT Edge 标记](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **用户名** | 容器注册表用户名 | 仅当针对专用注册表中的容器设置了 -AgentImage 参数时，才使用此参数。 提供有权访问注册表的用户名。 |
| **密码** | 安全密码字符串 | 仅当针对专用注册表中的容器设置了 -AgentImage 参数时，才使用此参数。 提供用于访问注册表的密码。 |

### <a name="update-iotedge"></a>Update-IoTEdge

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows**或**Linux** | 如果未指定容器 OS，则 Windows 是默认值。 对于 Windows 容器，安装中会包含一个容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 |
| **代理** | 代理 URL | 如果设备需要通过代理服务器来连接 Internet，请包含此参数。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目录路径 | 如果包含此参数，则安装程序将在列出的目录中检查安装时所需的 IoT Edge cab 和 VC 运行时 MSI 文件。 系统会下载该目录中不存在的任何文件。 如果这两个文件在该目录中存在，则无需建立 Internet 连接即可安装 IoT Edge。 还可以通过此参数来使用特定的版本。 |
| **RestartIfNeeded** | 无 | 此标志可让部署脚本在无提示的情况下根据需要重启计算机。 |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **团队** | 无 | 在上次尝试卸载失败时，此标志会强制卸载。
| **RestartIfNeeded** | 无 | 此标志可让卸载脚本在无提示的情况下根据需要重启计算机。 |

## <a name="next-steps"></a>后续步骤

预配 IoT Edge 设备和安装运行后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果无法正常安装 IoT Edge，请查看[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。
