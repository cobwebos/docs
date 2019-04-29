---
title: 在 Windows 上安装 Azure IoT Edge | Microsoft Docs
description: 有关在 Windows 10、Windows Server 和 Windows IoT Core 上安装 Azure IoT Edge 的说明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 95e984f6f08af01a2ffd7b9b4e0ec598d73f4d05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595098"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>在 Windows 上安装 Azure IoT Edge 运行时

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 

若要详细了解 IoT Edge 运行时，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了在 Windows x64 (AMD/Intel) 系统上安装 Azure IoT Edge 运行时的步骤。 Windows 支持目前为预览版。

> [!NOTE]
> 当 IoT Edge 模块（进程隔离的 Windows Nano Server 容器）正在运行时，一个已知的 Windows 操作系统问题会阻止转换到睡眠和休眠电源状态。 此问题会影响设备的电池寿命。
>
> 作为解决方法，在使用这些电源状态之前，请使用 `Stop-Service iotedge` 命令停止任何正在运行的 IoT Edge 模块。 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

不推荐或支持在 Windows 系统上使用 Linux 容器作为 Azure IoT Edge 的生产配置。 但可将其用于开发和测试。 

## <a name="prerequisites"></a>必备组件

参考本部分检查你的 Windows 设备是否支持 IoT Edge，并在安装之前为容器引擎准备好该设备。 

### <a name="supported-windows-versions"></a>支持的 Windows 版本

Azure IoT Edge 根据运行的是 Windows 容器还是 Linux 容器支持不同版本的 Windows。 

装有 Windows 容器的最新版 Azure IoT Edge 可在以下版本的 Windows 上运行：
* 装有 2018 年 10 月更新版（内部版本 17763）的 Windows 10 或 IoT Core
* Windows Server 2019（内部版本 17763）

装有 Linux 容器的最新版 Azure IoT Edge 可在以下版本的 Windows 上运行： 
* Windows 10 周年更新（内部版本 14393）或更高版本
* Windows Server 2016 或更高版本

有关目前支持哪些操作系统的详细信息，请参阅 [Azure IoT Edge 支持](support.md#operating-systems)。 

有关最新版 IoT Edge 包含的功能的详细信息，请参阅 [Azure IoT Edge 发行说明](https://github.com/Azure/azure-iotedge/releases)。

### <a name="prepare-for-a-container-engine"></a>为容器引擎做好准备 

Azure IoT Edge 依赖于 [OCI 兼容的](https://www.opencontainers.org/)容器引擎。 对于生产方案，请使用安装脚本中包含的 Moby 引擎在 Windows 设备上运行 Windows 容器。 对于开发和测试，可以在 Windows 设备上运行 Linux 容器，但需要在安装 IoT Edge 之前安装并配置容器。 对于任一方案，请参阅以下部分来了解准备设备时所要满足的先决条件。 

若要在虚拟机上安装 IoT Edge，请启用嵌套虚拟化并分配至少 2-GB 内存。 如何启用嵌套虚拟化取决于所用的虚拟机监控程序。 就 Hyper-V 来说，第 2 代虚拟机已默认启用嵌套虚拟化。 如果使用 VMWare，则可通过切换开关在虚拟机上启用此功能。 

#### <a name="moby-engine-for-windows-containers"></a>适用于 Windows 容器的 Moby 引擎

对于生产方案中运行 IoT Edge 的 Windows 设备而言，Moby 是唯一受支持的容器引擎。 在安装 IoT Edge 之前，安装脚本会自动在设备上安装 Moby 引擎。 通过启用“容器”功能来准备设备。 

1. 在“开始”栏中，搜索“打开或关闭 Windows 功能”并打开控制面板程序。
2. 找到“容器”并将其选中。
3. 选择“确定”。 

#### <a name="docker-for-linux-containers"></a>适用于 Linux 容器的 Docker

如果使用 Windows 开发和测试适用于 Linux 设备的容器，则可以使用[用于 Windows 的 Docker](https://www.docker.com/docker-windows) 作为容器引擎。 可将 Docker 配置为[使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。 需要在安装 IoT Edge 之前安装 Docker 并对其进行配置。 生产环境中的 Windows 设备不支持 Linux 容器。 

如果 IoT Edge 设备是 Windows 计算机，请检查它是否符合 Hyper-V 的[系统要求](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)。

## <a name="install-iot-edge-on-a-new-device"></a>在新设备上安装 IoT Edge

>[!NOTE]
>Azure IoT Edge 软件程序包受制于程序包中的许可条款（位于 LICENSE 目录中）。 使用程序包之前请阅读这些许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用包。

某个 PowerShell 脚本将下载并安装 Azure IoT Edge 安全守护程序。 然后，安全守护程序将启动两个运行时模块中的第一个，即 IoT Edge 代理，以便能够远程部署其他模块。 

首次在设备上安装 IoT Edge 运行时时，需要使用 IoT 中心内的标识预配该设备。 可以使用 IoT 中心提供的设备连接字符串手动预配单个 IoT Edge 设备。 或者，可以使用设备预配服务自动预配设备，需要设置多个设备时，这种做法非常有用。 根据预配选项，选择合适的安装脚本。 

以下部分介绍新设备上 IoT Edge 安装脚本的常见用例和参数。 

### <a name="option-1-install-and-manually-provision"></a>选项 1：安装和手动预配

在这第一个选项中，提供 IoT 中心生成的、用于预配设备的设备连接字符串。 

按照[注册新的 Azure IoT Edge 设备](how-to-register-device-portal.md)中的步骤注册设备并检索设备连接字符串。 

此示例演示使用 Windows 容器进行的手动安装：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

手动安装和预配设备时，可以使用附加的参数来修改安装，包括：
* 定向流量，使其通过代理服务器
* 将安装程序指向某个脱机目录
* 声明特定的代理容器映像，并提供凭据（如果该映像位于专用注册表中）
* 跳过 Moby CLI 安装

有关这些安装选项的详细信息，请继续阅读本文，或转到[所有安装参数](#all-installation-parameters)。

### <a name="option-2-install-and-automatically-provision"></a>选项 2：安装和自动预配

在这第二个选项中，使用 IoT 中心设备预配服务来预配设备。 提供设备预配服务实例中的“范围 ID”，以及设备中的“注册 ID”。

按照[在 Windows上创建和预配模拟 TPM Edge 设备](how-to-auto-provision-simulated-device-windows.md)中的步骤，设置设备预配服务并检索其**范围 ID**，模拟 TPM 设备并检索其**注册 ID**，然后创建个人注册。 在 IoT 中心注册设备后，继续安装。  

   >[!TIP]
   >在安装和测试期间，确保运行 TPM 模拟器的窗口处于打开状态。 

以下示例演示使用 Windows 容器进行的自动安装：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

手动安装和预配设备时，可以使用附加的参数来修改安装，包括：
* 定向流量，使其通过代理服务器
* 将安装程序指向某个脱机目录
* 声明特定的代理容器映像，并提供凭据（如果该映像位于专用注册表中）
* 跳过 Moby CLI 安装

有关这些安装选项的详细信息，请继续阅读本文，或转到[所有安装参数](#all-installation-parameters)。

## <a name="update-an-existing-installation"></a>更新现有安装

如果你以前已在设备上安装了 IoT Edge 运行时，并使用 IoT 中心内的标识对其做了预配，则可以使用一个简化的安装脚本。 标志 `-ExistingConfig` 声明设备上已存在一个 IoT Edge 配置文件。 该配置文件包含有关设备标识以及证书和网络设置的信息。 不管设备最初是手动还是自动预配的，都可以使用此安装选项。 

有关详细信息，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。

此示例演示指向现有配置文件并使用 Windows 容器的安装： 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

从现有配置文件安装 IoT Edge 时，可以使用附加的参数来修改安装，包括：
* 定向流量，使其通过代理服务器
* 将安装程序指向某个脱机目录，或者 
* 跳过 Moby CLI 安装。 

无法使用脚本参数声明 IoT Edge 代理容器映像，因为该信息已在前面的安装的配置文件中进行设置。 若要修改代理容器映像，请在 config.yaml 文件中修改。 

有关这些安装选项的详细信息，请继续阅读本文，或转到[所有安装参数](#all-installation-parameters)。

## <a name="offline-installation"></a>脱机安装

安装过程中将下载四个文件： 
* IoT Edge 安全守护程序 (iotedgd) zip 文件 
* Moby 引擎 zip 文件
* Moby CLI zip 文件
* Visual C++ Redistributable Package（VC 运行时）msi 文件

可以提前将其中的一个或所有文件下载到设备，然后将安装脚本指向包含这些文件的目录。 安装程序首先会检查该目录，然后仅下载未找到的组件。 如果所有四个文件都可脱机使用，则无需连接到 Internet 连接即可安装。 还可以使用此功能来替代一个或多个组件的联机版本。  

有关最新安装文件以及旧版本，请参阅 [Azure IoT Edge 发行说明](https://github.com/Azure/azure-iotedge/releases)

若要使用脱机组件进行安装，请使用 `-OfflineInstallationPath` 参数，并提供文件目录的绝对路径。 例如，

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>所有安装参数

前面几个部分介绍了常见安装方案，并举例说明了如何使用参数来修改安装脚本。 本部分提供了可用于安装 IoT Edge 的有效参数的参考表格。 如需更多信息，请在 PowerShell 窗口中运行 `get-help Install-SecurityDaemon -full`。 

若要使用现有配置安装 IoT Edge，安装命令可以使用以下常用参数： 

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **手动** | 无 | **开关参数**。 必须将每个安装声明为 manual、DPS 或 existingconfig。<br><br>声明你要提供设备连接字符串来手动预配设备 |
| **Dps** | 无 | **开关参数**。 必须将每个安装声明为 manual、DPS 或 existingconfig。<br><br>声明你要提供设备预配服务 (DPS) 范围 ID 和设备的注册 ID，以通过 DPS 进行预配。  |
| **ExistingConfig** | 无 | **开关参数**。 必须将每个安装声明为 manual、DPS 或 existingconfig。<br><br>声明设备上已存在 config.yaml 文件，其中包含设备的预配信息。 |
| **DeviceConnectionString** | 已在 IoT 中心注册的 IoT Edge 设备中的连接字符串，括在单引号中 | 对于手动安装，此参数是**必需**的。 如果未在脚本参数中提供连接字符串，则安装期间系统会提示你提供连接字符串。 |
| **ScopeId** | 与 IoT 中心关联的设备预配服务实例中的范围 ID。 | 对于 DPS 安装，此参数是**必需**的。 如果未在脚本参数中提供范围 ID，则安装期间系统会提示你提供范围 ID。 |
| **RegistrationId** | 设备生成的注册 ID | 对于 DPS 安装，此参数是**必需**的。 如果未在脚本参数中提供注册 ID，则安装期间系统会提示你提供注册 ID。 |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器 OS，将使用默认值 Linux。 对于 Windows 容器，安装中会包含一个容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 在 Windows 上运行 Linux 容器是一种有效的开发方案，但在生产环境中不受支持。 |
| **Proxy** | 代理 URL | 如果设备需要通过代理服务器来连接 Internet，请包含此参数。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目录路径 | 如果包含此参数，则安装程序将在目录中检查安装时所需的 iotedged zip 文件、Moby 引擎 zip 文件、Moby CLI zip 文件和 VC 运行时 MSI 文件。 如果所有四个文件都在该目录中，则可以脱机安装 IoT Edge。 还可以使用此参数来替代特定组件的联机版本。 |
| **AgentImage** | IoT Edge 代理映像 URI | 默认情况下，新的 IoT Edge 安装使用 IoT Edge 代理映像的最新滚动标记。 使用此参数可为映像版本设置特定的标记，或者提供自己的代理映像。 有关详细信息，请参阅[了解 IoT Edge 标记](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **用户名** | 容器注册表用户名 | 仅当针对专用注册表中的容器设置了 -AgentImage 参数时，才使用此参数。 提供有权访问注册表的用户名。 |
| **密码** | 安全密码字符串 | 仅当针对专用注册表中的容器设置了 -AgentImage 参数时，才使用此参数。 提供用于访问注册表的密码。 | 
| **SkipMobyCli** | 无 | 仅当 -ContainerOS 设置为 Windows 时才适用。 不会将 Moby CLI (docker.exe) 安装到 $MobyInstallDirectory。 |

## <a name="verify-successful-installation"></a>验证是否成功安装

可通过以下方式查看 IoT Edge 服务的状态： 

```powershell
Get-Service iotedge
```

使用下述项检查过去 5 分钟的服务日志：

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

此外，还可使用以下命令列出正在运行的模块：

```powershell
iotedge list
```

完成新的安装后，应会看到唯一运行的模块是 **edgeAgent**。 [部署 IoT Edge 模块](how-to-deploy-modules-portal.md)后，将会看到其他模块。 

## <a name="manage-module-containers"></a>管理模块容器

IoT Edge 服务要求在设备上运行容器引擎。 将模块部署到设备时，IoT Edge 运行时将使用容器引擎从云中的注册表提取容器映像。 IoT Edge 服务允许与模块交互和检索日志，但有时，你可能想要使用容器引擎来与容器本身交互。 

有关模块概念的详细信息，请参阅[了解 Azure IoT Edge 模块](iot-edge-modules.md)。 

如果在 Windows IoT Edge 设备上运行 Windows 容器，则 IoT Edge 安装中已包含 Moby 容器引擎。 如果你在 Windows 开发计算机上开发 Linux 容器，可能会使用 Docker Desktop。 Moby 引擎所基于的标准与 Docker 相同，可在 Docker Desktop 所在的同一台计算机上同时运行。 因此，若要以 Moby 引擎管理的容器为目标，则必须专门将该引擎指定为目标，而不要以 Docker 为目标。 

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
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

如果你打算在设备上重新安装 IoT Edge，请省略 `-DeleteConfig` 和 `-DeleteMobyDataRoot` 参数，以便将来可以使用现有的配置信息重新安装安全守护程序。 

## <a name="next-steps"></a>后续步骤

预配了安装运行时的 IoT Edge 设备后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果无法正常安装 IoT Edge，请查看[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。
