---
title: 从模块 Azure IoT Edge 使用 IoT Edge 设备本地存储 |Microsoft Docs
description: 使用环境变量和创建选项来启用对 IoT Edge 设备本地存储的模块访问。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2526f33f0053b5805394a4a898af88d86187066c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301277"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>向模块授予对设备的本地存储的访问权限

除了使用 Azure 存储服务或设备的容器存储中存储数据以外，还可以在主机上专门存储存储 IoT Edge 设备本身以提高可靠性，尤其是在脱机操作时。

若要在主机系统上设置存储，请为模块创建一个环境变量，该变量指向容器中的存储文件夹。 然后，使用创建选项将存储文件夹绑定到主机上的文件夹。

例如，如果你想要允许 IoT Edge 中心将消息存储在设备的本地存储中，并稍后检索它们，则可以在**配置高级边缘运行时设置中配置环境变量和 Azure 门户中的 "创建" 选项。** 部分。

1. 为 IoT Edge 中心和 IoT Edge 代理添加名为 **storageFolder** 的环境变量，使之指向模块中的目录。
1. 为 IoT Edge 中心和 IoT Edge 代理添加绑定，以便将主机上的本地目录连接到模块中的目录。 例如：

   ![为本地存储添加创建选项和环境变量](./media/how-to-access-host-storage-from-module/offline-storage.png)

也可直接在部署清单中配置本地存储。 例如：

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

将 `<HostStoragePath>` 和 `<ModuleStoragePath>` 替换为你的主机和模块存储路径；两个值都必须是绝对路径。

例如，在 Linux 系统上，`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 表示主机系统上的目录 **/etc/iotedge/storage**映射到容器中的目录 **/iotedge/storage/** 。 在 Windows 系统上，如另一个示例，`"Binds":["C:\\temp:C:\\contemp"]` 表示将主机系统上的目录**c： @no__t**映射到容器中的目录**c @no__t： 4contemp** 。

此外，在 Linux 设备上，请确保模块的用户配置文件具有主机系统目录的 "读取"、"写入" 和 "执行" 权限。 返回到允许 IoT Edge 中心将消息存储在设备的本地存储中的更早示例，需要为其用户配置文件 "UID 1000" 授予权限。 （IoT Edge 代理以 root 身份运行，因此它不需要其他权限。）可以通过多种方式来管理 Linux 系统上的目录权限，包括使用 `chown` 更改目录所有者，然后 `chmod` 更改权限，如：

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

可以从 [docker 文档](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)中找到有关如何创建选项的更多详细信息。
