---
title: 使用连接的工厂仪表板 - Azure | Microsoft Docs
description: 了解如何使用连接的工厂仪表板的功能。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 4af93b5667181941585cc1ac89f5696379962026
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075086"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>使用连接的工厂解决方案加速器仪表板中的功能

[部署基于云的解决方案来管理工业 IoT 设备](quickstart-connected-factory-deploy.md)快速入门演示了如何导航仪表板并响应警报。 本操作说明演示一些可用于监视和管理工业 IoT 设备的其他仪表板功能。

## <a name="apply-filters"></a>应用筛选器

可以在“工厂位置”面板或“警报”面板筛选仪表板上显示的信息：

1. 单击**漏斗**图标，在工厂位置面板或警报面板中显示可用筛选器的列表。

1. 此时会显示筛选器面板：

    [![连接的工厂解决方案加速器筛选器](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. 选择所需筛选器，单击“应用”。 也可在筛选字段中键入自定义文本。

1. 然后应用筛选器。 额外的漏斗图图标表示应用了筛选器：

    [![已应用连接的工厂解决方案加速器筛选器](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > 活动筛选器不影响显示的 OEE 和 KPI 值，仅筛选列表内容。

1. 要清除筛选器，请单击漏斗图，并单击筛选器面板中的“清除”。

## <a name="browse-an-opc-ua-server"></a>浏览 OPC UA 服务器

部署解决方案加速器时，会自动预配一组模拟 OPC UA 服务器，此类服务器可以从仪表板进行浏览。 模拟服务器可让用户轻松试验解决方案加速器，而不需要部署实际的服务器。 要将实际 OPC UA 服务器连接到解决方案，请参阅教程[将 OPC UA 设备连接到连接的工厂解决方案加速器](iot-accelerators-connected-factory-gateway-deployment.md)。

1. 单击仪表板导航栏中的“浏览器”图标：

    [![连接的工厂解决方案加速器服务器浏览器](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. 从显示在解决方案加速器中部署的服务器的列表选择一个服务器：

    [![连接的工厂解决方案加速器服务器列表](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. 单击“连接”，此时会显示一个安全对话框。 就此模拟来说，可以安全地单击“继续”。

1. 单击服务器树中的任意节点即可将其展开。 正在发布遥测的节点旁边有一个复选标记：

    [![连接的工厂解决方案加速器服务器树](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. 右键单击某个项，对该节点执行读取、写入、发布或调用操作。 能够使用哪些操作取决于权限和节点的属性。 读取选项会显示一个上下文面板，其中显示特定节点的值。 写入选项会显示一个上下文面板，可以在其中输入新值。 调用选项会显示一个节点，可以在其中输入调用的参数。

## <a name="publish-a-node"></a>发布节点

浏览“模拟 OPC UA 服务器”时，还可选择发布新节点。 可以在解决方案中分析这些节点的遥测。 使用这些模拟 OPC UA 服务器，可以轻松地试验解决方案加速器，不需部署实际的设备：

1. 浏览到要发布的 OPC UA 服务器浏览器树中的某个节点。

1. 右键单击该节点。 单击“发布”：

    [![连接的工厂解决方案加速器发布节点](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. 此时会显示一个上下文面板，告知你发布成功。 节点显示在装配站级别视图中，旁边有一个复选标记：

    [![连接的工厂解决方案加速器发布成功](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>命令和控制

连接工厂允许直接从云对工业设备发出命令并进行控制。 可以通过此功能来响应设备生成的警报。 例如，可以将命令发送到设备以打开泄压阀。 可以在 OPC UA 服务器浏览器树的 **StationCommands** 节点中查找可用命令。 在本方案中，需打开慕尼黑生产线装配站的泄压阀。 要使用命令和控制功能，必须具有“管理员”角色，可部署解决方案加速器：

1. 浏览到慕尼黑生产线 0 装配站的 OPC UA 服务器浏览器树中的 StationCommands 节点。

1. 选择要使用的命令。 右键单击 OpenPressureReleaseValve 节点。 单击“调用”：

    [![连接的工厂解决方案加速器调用命令](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. 此时会显示一个上下文面板，告知要调用的具体方法，以及任何参数详细信息。 单击“调用”：

    [![连接的工厂解决方案加速器调用参数](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. 上下文面板会进行更新，告知你该方法调用成功。 可以通过读取压力节点的值（在调用后已更新）来验证调用是否成功。

    [![连接的工厂解决方案加速器调用成功](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>幕后

部署解决方案加速器时，部署过程会在所选 Azure 订阅中创建多个资源。 可以在 Azure [门户](https://portal.azure.com)中查看这些资源。 部署过程会创建一个“资源组”，其名称基于为解决方案加速器选择的名称：

[![连接的工厂解决方案加速器资源组](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

可以查看每个资源的设置，方法是在资源组中的资源列表中选择该资源。

还可以查看 [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) GitHub 存储库中的资源解决方案加速器的源代码。

完成后，可在 [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) 站点的 Azure 订阅中删除解决方案加速器。 利用该站点，可以轻松地删除在创建解决方案加速器时预配的所有资源。

> [!NOTE]
> 要确保删除与解决方案加速器相关的所有内容，请在 [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) 站点中将其删除。 不要删除门户中的资源组。

## <a name="next-steps"></a>后续步骤

部署正常工作的解决方案加速器后，可以继续通过阅读以下文章开始使用 IoT 解决方案加速器：

* [连接的工厂解决方案加速器演练](iot-accelerators-connected-factory-sample-walkthrough.md)
* [将设备连接到连接的工厂解决方案加速器](iot-accelerators-connected-factory-gateway-deployment.md)
* [azureiotsolutions.com 站点权限](iot-accelerators-permissions.md)
