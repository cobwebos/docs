---
title: include 文件
description: include 文件
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/10/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b453a04a170764a037eed7415eaf71e5a4d37526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844576"
---
## <a name="deploy-event-grid-iot-edge-module"></a>部署事件网格 IoT 边缘模块

有几种方法可以将模块部署到 IoT 边缘设备，并且所有这些模块都适用于 IoT Edge 上的 Azure 事件网格。 本文介绍从 Azure 门户在 IoT 边缘上部署事件网格的步骤。

>[!NOTE]
> 在本教程中，您将部署事件网格模块，而不进行持久性。 这意味着在重新部署模块时，您在本教程中创建的任何主题和订阅都将被删除。 有关如何设置持久性的详细信息，请参阅以下文章[：Linux 中的持久状态](../articles/event-grid/edge/persist-state-linux.md)或[Windows 中的持久状态](../articles/event-grid/edge/persist-state-windows.md)。 对于生产工作负载，我们建议您安装具有持久性的事件网格模块。

>[!IMPORTANT]
> 在本教程中，事件网格模块将在客户端身份验证关闭的情况下部署，并允许 HTTP 订阅者。 对于生产工作负载，我们建议您仅启用启用客户端身份验证的 HTTPS 请求和订阅者。 有关如何安全地配置事件网格模块的详细信息，请参阅[安全性和身份验证](../articles/event-grid/edge/security-authentication.md)。
 
### <a name="select-your-iot-edge-device"></a>选择 IoT 边缘设备

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 导航到 IoT 中心。
1. 从 **"自动设备管理**"部分中的菜单中选择**IoT 边缘**。 
1. 从设备列表中单击目标设备的 ID
1. 选择**设置模块**。 保持页面打开。 您将继续下一节中的步骤。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供部署清单的创建向导，无需你手动构建 JSON 文档。  创建分为三步：添加模块、指定路由和评审部署************。

### <a name="add-modules"></a>添加模块

1. 在 **"部署模块"** 部分中，选择 **"添加**
1. 从下拉列表中的模块类型中，选择**IoT 边缘模块**
1. 提供容器的名称、图像、容器创建选项：

[!INCLUDE [event-grid-edge-module-version-update](event-grid-edge-module-version-update.md)]

   * **名称**： 事件网格模块
   * **图像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器创建选项**：

    ```json
        {
          "Env": [
            "inbound__clientAuth:clientCert__enabled=false",
            "outbound__webhook__httpsOnly=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                  "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```

 1. 单击 **“保存”**
 1. 单击 **"下一步**"以继续到路径部分

    > [!NOTE]
    > 如果使用 Azure VM 作为边缘设备，请添加入站端口规则以允许端口 4438 上的入站流量。 有关添加规则的说明，请参阅[如何将端口打开到 VM](../articles/virtual-machines/windows/nsg-quickstart-portal.md)。


### <a name="setup-routes"></a>设置路由

 保留默认路由，然后选择 **"下一步**"以继续查看部分

### <a name="review-deployment"></a>评审部署

1. 评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 确认您看到列表中的两个模块 **：$edgeAgent**和 **$edgeHub**。 这两个模块构成 IoT Edge 运行时，且是每个部署中所需的默认设置。
1. 审阅部署信息，然后选择“提交”****。

### <a name="verify-your-deployment"></a>验证部署

1. 提交部署后，返回到 IoT 中心的“IoT Edge”页。
1. 选择部署时针对的**IoT Edge 设备**以打开其详细信息。
1. 在设备详细信息中，验证事件网格模块是否列为 **"部署中指定**"和 **"按设备报告**"。

可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。