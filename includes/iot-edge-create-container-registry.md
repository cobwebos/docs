---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 0c43c6dcced94225e9ab9ae903535ce74286ad9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87406702"
---
## <a name="create-a-container-registry"></a>创建容器注册表

本教程将使用 Azure IoT Tools 扩展来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。

可以使用任意兼容 Docker 的注册表来保存容器映像。 两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。

如果还没有容器注册表，请执行以下步骤，以便在 Azure 中创建一个新的：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”   >   “容器” >   “容器注册表”。

2. 提供以下值，以便创建容器注册表：

   | 字段 | 值 |
   | ----- | ----- |
   | 订阅 | 从下拉列表中选择“订阅”。 |
   | 资源组 | 建议对在 IoT Edge 快速入门和教程中创建的所有测试资源使用同一资源组。 例如，**IoTEdgeResources**。 |
   | 注册表名称 | 提供唯一名称。 |
   | 位置 | 选择靠近你的位置。 |
   | SKU | 选择“基本”。 |

3. 选择“创建”。

4. 创建容器注册表后，浏览到它，然后从左窗格中，选择“设置”下菜单中的“访问密钥” 。 

5. 单击以允许管理员用户查看容器注册表的“用户名”和“密码”。

6. 复制“登录服务器”、“用户名”和“密码”的值，并将其保存在方便的位置  。 本教程将使用这些值来访问容器注册表。

   ![复制容器注册表的登录服务器、用户名和密码](./media/iot-edge-create-container-registry/registry-access-key.png)