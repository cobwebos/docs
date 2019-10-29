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
ms.openlocfilehash: fab9a8a8c28f2f75e7e5af69b70229c1de74c684
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992283"
---
## <a name="deploy-event-grid-iot-edge-module"></a>部署事件网格 IoT Edge 模块

可以通过多种方式将模块部署到 IoT Edge 设备，并且所有这些模块都适用于 IoT Edge 上的 Azure 事件网格。 本文介绍了在 Azure 门户中 IoT Edge 部署事件网格的步骤。

>[!NOTE]
> 在本教程中，您将部署不带持久性的事件网格模块。 这意味着，在您重新部署该模块时，将删除您在本教程中创建的任何主题和订阅。 有关如何设置持久性的详细信息，请参阅以下文章：[在 Linux 中持久保存状态](../articles/event-grid/edge/persist-state-linux.md)或[在 Windows 中保持状态](../articles/event-grid/edge/persist-state-windows.md)。 对于生产工作负荷，我们建议您安装带有持久性的事件网格模块。

>[!IMPORTANT]
> 在本教程中，将在客户端身份验证关闭的情况下部署事件网格模块，并允许 HTTP 订阅服务器。 对于生产工作负荷，建议仅启用启用了客户端身份验证的 HTTPS 请求和订阅服务器。 有关如何安全配置事件网格模块的详细信息，请参阅[安全性和身份验证](../articles/event-grid/edge/security-authentication.md)。

### <a name="select-your-iot-edge-device"></a>选择 IoT Edge 设备

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 导航到 IoT 中心。
1. 从 "**自动设备管理**" 部分的菜单中选择 " **IoT Edge** "。 
1. 从设备列表中单击目标设备的 ID
1. 选择“设置模块”。 使页面保持打开状态。 你将继续执行下一节中的步骤。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供了一个向导，该向导将引导你创建部署清单，而不是手动生成 JSON 文档。  创建分为三步：添加模块、指定路由和评审部署。

### <a name="add-modules"></a>添加模块

1. 在 "**部署模块**" 部分，选择 "**添加**"
1. 从下拉列表中的模块类型中，选择 " **IoT Edge 模块**
1. 提供容器的名称、图像、容器创建选项：

   * **名称**： eventgridmodule
   * **映像 URI**： `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器创建选项**：

    ```json
        {
          "Env": [
            "inbound:clientAuth:clientCert:enabled=false",
            "outbound:webhook:httpsOnly=false"
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

 1. 单击“保存”
 1. 单击 "**下一步**" 以继续转到 "路由" 部分

### <a name="setup-routes"></a>安装路由

 保留默认路由，然后选择 "**下一步**" 继续查看部分

### <a name="review-deployment"></a>评审部署

1. 评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 确认列表中显示了两个模块： " **$edgeAgent** " 和 " **$edgeHub**"。 这两个模块组成 IoT Edge 运行时，每个部署中都需要默认值。
1. 审阅部署信息，然后选择“提交”。

### <a name="verify-your-deployment"></a>验证你的部署

1. 提交部署后，返回到 IoT 中心的 "IoT Edge" 页。
1. 选择部署目标**IoT Edge 设备**，以打开其详细信息。
1. 在 "设备详细信息" 中，验证 "事件网格" 模块是否作为 **"部署" 中指定**的和 "**设备" 报告**。

可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。