---
author: baanders
description: Azure 数字孪生的包含文件 - 设置最新的 IoT 扩展
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606942"
---
首先，运行此命令来查看已安装的所有扩展的列表。

```azurecli
az extension list
```

输出是你当前拥有的所有扩展的数组。 查找每个列表项的 `"name"` 字段，以查看扩展的名称。

使用输出确定要为扩展安装程序运行的以下哪个命令（可以运行多个）。
* 如果列表包含 `azure-iot`：你已拥有该扩展。 运行此命令以确保你拥有最新更新且没有其他更新可用：

   ```azurecli
   az extension update --name azure-iot
   ```

* 如果列表不包含 `azure-iot`：需要安装扩展。 使用此命令：

    ```azurecli
    az extension add --name azure-iot
    ```

* 如果列表包含 `azure-iot-cli-ext`：这是该扩展的旧版本。 一次只能安装该扩展的一个版本，因此应卸载旧扩展。 使用此命令：

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```