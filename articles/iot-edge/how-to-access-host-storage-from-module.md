---
title: 通过模块使用 IoT Edge 设备本地存储 - Azure IoT Edge | Microsoft Docs
description: 使用环境变量和创建选项来启用对 IoT Edge 设备本地存储的模块访问。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4af63421e831318e6250825cffd1abad415b85bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447838"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>向模块授予对设备本地存储的访问权限

除了使用 Azure 存储服务或在设备的容器存储中存储数据外，还可以将存储专用于主机 IoT Edge 设备本身，以提高可靠性，特别是在脱机操作时。

## <a name="link-module-storage-to-device-storage"></a>将模块存储链接到设备存储

若要启用从模块存储到主机系统上的存储的链接，请为模块创建指向容器中存储文件夹的环境变量。 然后，使用创建选项将存储文件夹绑定到主机上的文件夹。

例如，如果要启用 IoT Edge 中心以将消息存储在设备的本地存储中并在以后检索它们，可以在 Azure 门户的“运行时设置”部分中配置环境变量和创建选项  。

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

例如，在 Linux 系统上，`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 表示主机系统上的目录 /etc/iotedge/storage  映射到容器中的目录 /iotedge/storage/  。 再举一个例子，在 Windows 系统上，`"Binds":["C:\\temp:C:\\contemp"]` 表示主机系统上的目录 **C:\\temp** 映射到容器中的目录 **C:\\contemp**。

另外，在 Linux 设备上，确保模块的用户配置文件对主机系统目录具有所需的读取、写入和执行权限。 回到启用 IoT Edge 中心以将消息存储到设备的本地存储的先前示例，你需要向其用户配置文件（UID 为 1000）授予权限。 （IoT Edge 代理以根用户身份运行，因此不需额外的权限。）在 Linux 系统上，可以通过多种方式管理目录权限，包括使用 `chown` 来更改目录所有者，然后使用 `chmod` 来更改权限，例如：

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

可以从 [docker 文档](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)中找到有关如何创建选项的更多详细信息。

## <a name="encrypted-data-in-module-storage"></a>模块存储中的加密数据

当模块调用 IoT Edge 守护程序的工作负载 API 来加密数据时，系统会使用模块 ID 和模块的生成 ID 来派生加密密钥。 如果从部署中删除了某个模块，然后将另一具有相同模块 ID 的模块部署到同一设备，则会使用生成 ID 来保护机密。 可以使用 Azure CLI 命令 [az iot hub module-identity show](/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-identity#ext-azure-cli-iot-ext-az-iot-hub-module-identity-show) 查看模块的生成 ID。

如果要跨代在模块之间共享文件，则这些文件不能包含任何机密，否则无法解密。

## <a name="next-steps"></a>后续步骤

有关从模块访问主机存储的其他示例，请参阅[使用 IoT Edge 上的 Azure Blob 存储在边缘存储数据](how-to-store-data-blob.md)。
