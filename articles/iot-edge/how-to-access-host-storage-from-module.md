---
title: 从模块 Azure IoT Edge 使用 IoT Edge 设备本地存储 |Microsoft Docs
description: 使用环境变量和创建选项来启用对 IoT Edge 设备本地存储的模块访问。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 72fb7cfad5683edeb3b3335c28c53a7e693d00d5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330801"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>向模块授予对设备本地存储的访问权限

除了使用 Azure 存储服务或设备的容器存储中存储数据以外，还可以在主机上专门存储存储 IoT Edge 设备本身以提高可靠性，尤其是在脱机操作时。

## <a name="link-module-storage-to-device-storage"></a>将模块存储链接到设备存储

若要启用从模块存储到主机系统上存储的链接，请为模块创建一个环境变量，该变量指向容器中的存储文件夹。 然后，使用创建选项将存储文件夹绑定到主机上的文件夹。

例如，如果你想要允许 IoT Edge 中心将消息存储在设备的本地存储中，并稍后检索它们，则可以在**配置高级边缘运行时设置中配置环境变量和 Azure 门户中的 "创建" 选项。** 部分。

1. 对于 IoT Edge 中心和 IoT Edge 代理，添加一个名为**storageFolder**的环境变量，指向模块中的一个目录。
1. 对于 IoT Edge 集线器和 IoT Edge 代理，添加 "绑定"，将主计算机上的本地目录连接到模块中的一个目录。 例如：

   ![为本地存储添加创建选项和环境变量](./media/how-to-access-host-storage-from-module/offline-storage.png)

或者，你可以在部署清单中直接配置本地存储。 例如：

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

将 `<HostStoragePath>` 替换为主机和模块存储路径，`<ModuleStoragePath>`;这两个值必须是绝对路径。

例如，在 Linux 系统上，`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 表示主机系统上的目录 **/etc/iotedge/storage**映射到容器中的目录 **/iotedge/storage/** 。 在 Windows 系统上，如另一个示例，`"Binds":["C:\\temp:C:\\contemp"]` 表示将主机系统上的目录**c： @no__t**映射到容器中的目录**c @no__t： 4contemp** 。

此外，在 Linux 设备上，请确保模块的用户配置文件具有主机系统目录的 "读取"、"写入" 和 "执行" 权限。 返回到允许 IoT Edge 中心将消息存储在设备的本地存储中的更早示例，需要为其用户配置文件 "UID 1000" 授予权限。 （IoT Edge 代理以 root 身份运行，因此它不需要其他权限。）可以通过多种方式来管理 Linux 系统上的目录权限，包括使用 `chown` 更改目录所有者，然后 `chmod` 更改权限，如：

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

可以在[docker 文档](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)中找到有关创建选项的更多详细信息。

## <a name="next-steps"></a>后续步骤

有关从模块访问主机存储的其他示例，请参阅[在 IoT Edge 上使用 Azure Blob 存储将数据存储在边缘](how-to-store-data-blob.md)。
