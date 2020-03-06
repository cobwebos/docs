---
title: 在 Windows 上安装 Azure IoT Edge | Microsoft Docs
description: 有关在 Windows 10、Windows Server 和 Windows IoT Core 上安装 Azure IoT Edge 的说明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.openlocfilehash: e3f55f9be28a8b53f012e111e43ba1f495b1d585
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395691"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>在 Windows 上安装 Azure IoT Edge 运行时

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。

若要详细了解 IoT Edge 运行时，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了使用 Windows 容器在 Windows x64 （AMD/Intel）系统上安装 Azure IoT Edge 运行时的步骤。

> [!NOTE]
> 已知的 Windows 操作系统问题会阻止在运行 IoT Edge 模块（由进程隔离的 Windows Nano Server 容器）时转换为睡眠和休眠电源状态。 此问题会影响设备上的电池寿命。
>
> 解决方法之一是，使用命令 `Stop-Service iotedge` 停止任何正在运行的 IoT Edge 模块，然后再使用这些电源状态。

不推荐或支持在 Windows 系统上使用 Linux 容器作为 Azure IoT Edge 的生产配置。 但可将其用于开发和测试。 若要了解详细信息，请参阅[使用 Windows 上的 IoT Edge 运行 Linux 容器](how-to-install-iot-edge-windows-with-linux.md)。

有关 IoT Edge 最新版本中包含的内容的信息，请参阅[Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="prerequisites"></a>必备条件

参考本部分检查你的 Windows 设备是否支持 IoT Edge，并在安装之前为容器引擎准备好该设备。

### <a name="supported-windows-versions"></a>支持的 Windows 版本

对于开发和测试方案，可以在支持容器功能的任何版本的 Windows 10 或 Windows Server 2019 （版本17763）上安装 Windows 容器 Azure IoT Edge。 有关生产方案当前支持的操作系统的信息，请参阅[Azure IoT Edge 支持的系统](support.md#operating-systems)。

IoT 核心设备必须包含 IoT Core-Windows 容器可选功能以支持 IoT Edge 运行时。 在[远程 PowerShell 会话](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)中使用以下命令来检查设备上是否支持 Windows 容器：

```powershell
Get-Service vmcompute
```

如果该服务存在，则应在服务状态显示为 "**正在运行**" 时获得成功的响应。 如果找不到 vmcompute 服务，则设备不满足 IoT Edge 的要求。 请与你的硬件提供商联系，询问此功能的支持。

### <a name="prepare-for-a-container-engine"></a>为容器引擎做好准备

Azure IoT Edge 依赖于 [OCI 兼容的](https://www.opencontainers.org/)容器引擎。 对于生产方案，请使用安装脚本中包含的 Moby 引擎在 Windows 设备上运行 Windows 容器。

## <a name="install-iot-edge-on-a-new-device"></a>在新设备上安装 IoT Edge

>[!NOTE]
>Azure IoT Edge 软件程序包受制于程序包中的许可条款（位于 LICENSE 目录中）。 使用程序包之前请阅读这些许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用程序包。

某个 PowerShell 脚本将下载并安装 Azure IoT Edge 安全守护程序。 然后，安全守护程序将启动两个运行时模块中的第一个，即 IoT Edge 代理，以便能够远程部署其他模块。

>[!TIP]
>对于 IoT Core 设备，我们建议使用 RemotePowerShell 会话运行安装命令。 有关详细信息，请参阅将[PowerShell 用于 Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)。

首次在设备上安装 IoT Edge 运行时时，需要使用 IoT 中心内的标识预配该设备。 可以使用 IoT 中心提供的设备连接字符串手动设置单个 IoT Edge 设备。 或者，你可以使用设备预配服务（DPS）自动设置设备，当你有许多设备需要设置时，这非常有用。 根据预配选项，选择合适的安装脚本。

以下部分介绍新设备上 IoT Edge 安装脚本的常见用例和参数。

### <a name="option-1-install-and-manually-provision"></a>选项 1：安装和手动预配

在第一个选项中，你将提供 IoT 中心生成的**设备连接字符串**来设置设备。

此示例演示了使用 Windows 容器进行的手动安装：

1. 如果尚未注册，请注册新的 IoT Edge 设备并检索**设备连接字符串**。 复制此部分后面要使用的连接字符串。 您可以使用以下工具来完成此步骤：

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

3. **IoTEdge**命令检查 Windows 计算机是否在受支持的版本上，打开容器功能，然后下载小鲸鱼运行时和 IoT Edge 运行时。 命令默认为使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. 此时，IoT 核心设备可能会自动重新启动。 其他 Windows 10 或 Windows Server 设备可能会提示您重新启动。 如果是这样，请立即重新启动设备。 设备准备就绪后，请再次以管理员身份运行 PowerShell。

5. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. 出现提示时，请提供在步骤1中检索到的设备连接字符串。 设备连接字符串将物理设备与 IoT 中心中的设备 ID 相关联。

   设备连接字符串采用以下格式，不应包括引号： `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. 使用[验证成功安装](#verify-successful-installation)中的步骤检查设备上的 IoT Edge 的状态。

手动安装和预配设备时，可以使用附加的参数来修改安装，包括：

* 定向流量，使其通过代理服务器
* 将安装程序指向某个脱机目录
* 声明特定的代理容器映像，并提供凭据（如果该映像位于专用注册表中）

有关这些安装选项的详细信息，请直接跳到了解[所有安装参数](#all-installation-parameters)。

### <a name="option-2-install-and-automatically-provision"></a>选项 2：安装和自动预配

在这第二个选项中，使用 IoT 中心设备预配服务来预配设备。 提供设备预配服务实例的**作用域 ID**以及特定于你首选[证明机制](../iot-dps/concepts-security.md#attestation-mechanism)的任何其他信息：

* [使用 Windows 上的虚拟 TPM 创建和预配模拟 IoT Edge 设备](how-to-auto-provision-simulated-device-windows.md)
* [使用对称密钥证明创建和预配 IoT Edge 设备](how-to-auto-provision-symmetric-keys.md)

自动安装和设置设备时，可以使用其他参数来修改安装，包括：

* 定向流量，使其通过代理服务器
* 将安装程序指向某个脱机目录
* 声明特定的代理容器映像，并提供凭据（如果该映像位于专用注册表中）

有关这些安装选项的详细信息，请继续阅读本文，或转到[所有安装参数](#all-installation-parameters)。

## <a name="offline-or-specific-version-installation"></a>脱机或特定版本安装

在安装期间，下载两个文件：

* Microsoft Azure IoT Edge cab，其中包含 IoT Edge 安全守护程序（iotedged）、小鲸鱼容器引擎和小鲸鱼 CLI。
* Visual C++可再发行组件包（VC 运行时） MSI

如果在安装过程中设备将处于脱机状态，或者，如果要安装 IoT Edge 的特定版本，则可以提前将其中一个或两个文件下载到设备。 当安装时，请将安装脚本指向包含已下载文件的目录。 安装程序首先检查该目录，然后仅下载未找到的组件。 如果所有文件都可脱机使用，则可以在不使用 internet 连接的情况下安装。

有关最新 IoT Edge 安装文件以及早期版本，请参阅[Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

若要安装脱机组件，请使用 `-OfflineInstallationPath` 参数作为 IoTEdge 命令的一部分，并提供文件目录的绝对路径。 例如，

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>`-OfflineInstallationPath` 参数将在提供的目录中查找名为**Microsoft-Azure-IoTEdge**的文件。 从 IoT Edge 1.0.9 版本开始，可以使用两个 .cab 文件，一个用于 AMD64 设备，另一个用于 ARM32。 下载适用于你的设备的正确文件，然后重命名该文件以删除体系结构后缀。

`Deploy-IoTEdge` 命令安装 IoT Edge 组件，然后需要继续执行 `Initialize-IoTEdge` 命令，以将设备设置为 IoT 中心设备 ID 和连接。 直接运行命令并提供 IoT 中心的连接字符串，或者使用上一节中的链接之一，了解如何使用设备预配服务自动设置设备。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

还可以将脱机安装路径参数与 IoTEdge 命令一起使用。

## <a name="verify-successful-installation"></a>验证是否成功安装

检查 IoT Edge 服务的状态。 它应列为 "正在运行"。  

```powershell
Get-Service iotedge
```

检查过去 5 分钟的服务日志。 如果刚完成了 IoT Edge 运行时的安装，则可能会在运行 **Deploy-IoTEdge** 和**Initialize-IoTEdge** 之间看到一系列的错误。 这些错误是预期的错误，因为服务在配置前尝试启动。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

对最常见的配置和网络错误运行自动检查。

```powershell
iotedge check
```

列出正在运行的模块。 在全新安装之后，应该看到运行的唯一模块是 **edgeAgent**。 首次[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)后，其他系统模块**edgeHub**也会在设备上启动。

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>管理模块容器

IoT Edge 服务要求容器引擎在设备上运行。 将模块部署到设备时，IoT Edge 运行时使用容器引擎从云中的注册表提取容器映像。 IoT Edge 服务使你可以与你的模块进行交互，并检索日志，但有时你可能希望使用容器引擎来与容器本身交互。

有关模块概念的详细信息，请参阅[了解 Azure IoT Edge 模块](iot-edge-modules.md)。

如果在 Windows IoT Edge 设备上运行 Windows 容器，则 IoT Edge 安装包括小鲸鱼容器引擎。 小鲸鱼引擎基于与 Docker 相同的标准，并且设计为在 Docker Desktop 所在的同一台计算机上并行运行。 出于此原因，如果想要以小鲸鱼引擎管理的容器为目标，则必须专门将该引擎定位到该引擎，而不是 Docker。

例如，若要列出所有 Docker 映像，请使用以下命令：

```powershell
docker images
```

若要列出所有小鲸鱼映像，请使用小鲸鱼引擎的指针修改同一命令：

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

引擎 URI 在安装脚本的输出中列出，或者您可以在 yaml 文件的 "容器运行时设置" 部分中找到它。

![yaml 中的 moby_runtime uri](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

有关可用于与设备上运行的容器和图像交互的命令的详细信息，请参阅[Docker 命令行接口](https://docs.docker.com/engine/reference/commandline/docker/)。

## <a name="uninstall-iot-edge"></a>卸载 IoT Edge

若要从 Windows 设备中删除 IoT Edge 安装，请在 PowerShell 管理窗口中使用以下命令。 此命令会删除 IoT Edge 运行时，以及现有的配置和 Moby 引擎数据。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

IoTEdge 命令在 Windows IoT Core 上不起作用。 若要从 Windows IoT Core 设备中删除 IoT Edge，需要重新部署 Windows IoT Core 映像。

有关卸载选项的详细信息，请使用命令 `Get-Help Uninstall-IoTEdge -full`。

## <a name="all-installation-parameters"></a>所有安装参数

前面几个部分介绍了常见安装方案，并举例说明了如何使用参数来修改安装脚本。 本部分提供用于安装、更新或卸载 IoT Edge 的通用参数的参考表。

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

IoTEdge 命令将下载并部署 IoT Edge 安全守护程序及其依赖项。 部署命令接受这些通用参数，以及其他参数。 有关完整列表，请使用命令 `Get-Help Deploy-IoTEdge -full`。  

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器操作系统，则 Windows 为默认值。<br><br>对于 Windows 容器，IoT Edge 使用安装中包含的小鲸鱼容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 |
| **Proxy** | 代理 URL | 如果设备需要通过代理服务器来连接 Internet，请包含此参数。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目录路径 | 如果包含此参数，则安装程序将在列出的目录中检查安装所需的 IoT Edge cab 和 VC 运行时 MSI 文件。 未在目录中找到的所有文件都将被下载。 如果这两个文件都在目录中，则无需 internet 连接即可安装 IoT Edge。 你还可以使用此参数来使用特定版本。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **RestartIfNeeded** | none | 此标志允许部署脚本在不提示的情况下重新启动计算机（如有必要）。 |

### <a name="initialize-iotedge"></a>IoTEdge

IoTEdge 命令将 IoT Edge 配置为设备连接字符串和操作详细信息。 然后，此命令生成的大部分信息都存储在 iotedge\config.yaml 文件中。 初始化命令接受这些公共参数，以及其他参数。 有关完整列表，请使用命令 `Get-Help Initialize-IoTEdge -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **手动** | 无 | **开关参数**。 如果未指定设置类型，则手动为默认值。<br><br>声明你要提供设备连接字符串来手动预配设备 |
| **Dps** | 无 | **开关参数**。 如果未指定设置类型，则手动为默认值。<br><br>声明你要提供设备预配服务 (DPS) 范围 ID 和设备的注册 ID，以通过 DPS 进行预配。  |
| **DeviceConnectionString** | 已在 IoT 中心注册的 IoT Edge 设备中的连接字符串，括在单引号中 | 对于手动安装，此参数是**必需**的。 如果未在脚本参数中提供连接字符串，则安装期间系统会提示你提供连接字符串。 |
| **ScopeId** | 与 IoT 中心关联的设备预配服务实例中的范围 ID。 | 对于 DPS 安装，此参数是**必需**的。 如果未在脚本参数中提供范围 ID，则安装期间系统会提示你提供范围 ID。 |
| **RegistrationId** | 设备生成的注册 ID | 如果使用 TPM 或对称密钥证明，则**需要**安装 DPS。 |
| **SymmetricKey** | 使用 DPS 时用于预配 IoT Edge 设备标识的对称密钥 | 如果使用对称密钥证明，则**需要**安装 DPS。 |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器操作系统，则 Windows 为默认值。<br><br>对于 Windows 容器，IoT Edge 使用安装中包含的小鲸鱼容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **AgentImage** | IoT Edge 代理映像 URI | 默认情况下，新的 IoT Edge 安装使用 IoT Edge 代理映像的最新滚动标记。 使用此参数可为映像版本设置特定的标记，或者提供自己的代理映像。 有关详细信息，请参阅[了解 IoT Edge 标记](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **用户名** | 容器注册表用户名 | 仅当针对专用注册表中的容器设置了 -AgentImage 参数时，才使用此参数。 提供有权访问注册表的用户名。 |
| **密码** | 安全密码字符串 | 仅当针对专用注册表中的容器设置了 -AgentImage 参数时，才使用此参数。 提供用于访问注册表的密码。 |

### <a name="update-iotedge"></a>Update-IoTEdge

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器 OS，则 Windows 为默认值。 对于 Windows 容器，安装中会包含一个容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 |
| **Proxy** | 代理 URL | 如果设备需要通过代理服务器来连接 Internet，请包含此参数。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目录路径 | 如果包含此参数，则安装程序将在列出的目录中检查安装所需的 IoT Edge cab 和 VC 运行时 MSI 文件。 未在目录中找到的所有文件都将被下载。 如果这两个文件都在目录中，则无需 internet 连接即可安装 IoT Edge。 你还可以使用此参数来使用特定版本。 |
| **RestartIfNeeded** | none | 此标志允许部署脚本在不提示的情况下重新启动计算机（如有必要）。 |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **团队** | none | 此标志会强制卸载，以防上一次卸载尝试失败。
| **RestartIfNeeded** | none | 此标志允许卸载脚本在不提示的情况下重新启动计算机（如有必要）。 |

## <a name="next-steps"></a>后续步骤

预配了安装运行时的 IoT Edge 设备后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果无法正常安装 IoT Edge，请查看[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。
