---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453717"
---
## <a name="clean-up-resources"></a>清理资源

如果打算继续学习其他 IoT 即插即用文章，则可保留并重复使用在本快速入门中使用的资源。 否则，可删除在本快速入门中创建的资源，以免产生额外的费用。

可通过使用以下 Azure CLI 命令删除整个资源组来一次性删除中心和注册设备。 （但是，如果这些资源与用于其他目的的其他资源共享资源组，则不要使用此方法。）

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
若要仅删除 IoT 中心，请使用 Azure CLI 运行以下命令：

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

若要仅删除注册到 IoT 中心的设备标识，请使用 Azure CLI 运行以下命令：

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

你可能还需要从开发计算机中删除克隆的示例文件。