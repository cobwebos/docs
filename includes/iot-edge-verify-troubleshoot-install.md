---
title: 安装和预配后，验证是否成功以及如何进行故障排除
description: include 文件
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979439"
---
## <a name="verify-successful-setup"></a>验证安装是否成功

检查 IoT Edge 服务的状态。 该服务应列为“正在运行”。  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

检查服务日志。

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

如果刚刚完成了 IoT Edge 运行时的安装，可能会看到运行 **Deploy-IoTEdge** 之后、运行 **Initialize-IoTEdge** 之前发生的错误列表。 这些错误在预料之中，因为该服务在经过配置之前会尝试启动。

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

运行[故障排除工具](../articles/iot-edge/troubleshoot.md#run-the-check-command)，检查最常见的配置和网络错误。

```powershell
iotedge check
```

在将第一个模块部署到设备上的 IoT Edge 之前， **$edgeHub** 系统模块不会部署到设备。 因此，自动检查会返回一个针对 `Edge Hub can bind to ports on host` 连接性检查的错误。 此错误可以忽略，除非它是在将模块部署到设备后发生的。

最后，列出正在运行的模块：

```powershell
iotedge list
```

完成新的安装后，应会看到唯一运行的模块是 **edgeAgent**。

## <a name="tips-and-troubleshooting"></a>提示和故障排除

在资源受限的设备上，强烈建议按照[故障排除指南](../articles/iot-edge/troubleshoot.md)中的说明将 *OptimizeForPerformance* 环境变量设置为 *false*。

如果设备无法连接到 IoT 中心，并且你的网络具有代理服务器，请按照 [将 IoT Edge 设备配置为通过代理服务器进行通信](../articles/iot-edge/how-to-configure-proxy-support.md)中的步骤进行操作。

# <a name="linux"></a>[Linux](#tab/linux)

在 Linux 设备上，需要提升的权限才能运行 `iotedge` 命令。 安装运行时后，请从计算机中注销并重新登录以自动更新权限。 在此之前，请使用 `sudo` 以提升的权限运行命令。

# <a name="windows"></a>[Windows](#tab/windows)

在运行 Windows 容器的 Windows 设备上，小鲸鱼容器引擎作为 IoT Edge 的一部分安装。 小鲸鱼引擎旨在与 Docker Desktop 并行运行。 `docker`如果要与设备上的容器直接交互，则可以使用命令。 但是，在设备上还安装了 Docker 桌面的情况下，必须专门以小鲸鱼引擎为目标。

例如，若要列出所有 Docker 映像，可使用以下命令：

```powershell
docker images
```

若要列出所有 Moby 映像，可以使用指向 Moby 引擎的指针修改上述命令：

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

引擎 URI 将在安装脚本的输出中列出，也可以在 config.yaml 文件的容器运行时设置节中找到它。

![config.yaml 中的 moby_runtime uri](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
