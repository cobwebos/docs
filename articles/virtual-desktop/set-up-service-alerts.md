---
title: 为 Windows 虚拟桌面设置服务警报 - Azure
description: 如何设置 Azure 服务运行状况以接收 Windows 虚拟桌面的服务通知。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: effe3125a2d41f0410a1a0b3a87c41da39feb6d9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291350"
---
# <a name="tutorial-set-up-service-alerts"></a>教程：设置服务警报

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/set-up-service-alerts-2019.md)。

可以使用 Azure 服务运行状况监视 Windows 虚拟桌面的服务问题和运行状况公告。 Azure 服务运行状况可以通过不同类型的警报（例如，电子邮件或短信）通知你，帮助你了解问题的影响，并在问题解决时保持更新。 此外，Azure 服务运行状况还可以帮助你减少停机时间，并为可能影响资源可用性的计划内维护和更改做准备。

本教程介绍以下操作：

> [!div class="checklist"]
> * 创建和配置服务警报。

若要详细了解 Azure 服务运行状况，请参阅 [Azure 运行状况文档](https://docs.microsoft.com/azure/service-health/)。

## <a name="create-service-alerts"></a>创建服务警报

本部分介绍如何配置 Azure 服务运行状况以及如何设置通知，你可以在 Azure 门户上访问这些通知。 可以设置不同类型的警报，并安排它们及时通知你。

### <a name="recommended-service-alerts"></a>建议的服务警报

我们建议你为以下运行状况事件类型创建服务警报：

- **服务问题：** 接收有关影响用户与服务的连接或管理 Windows 虚拟桌面租户的能力的主要问题的通知。
- **运行状况公告：** 接收需要你注意的通知。 以下是此类通知的一些示例：
    - 虚拟机 (VM) 没有安全配置为打开端口 3389
    - 弃用的功能

### <a name="configure-service-alerts"></a>配置服务警报

若要配置服务警报，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择“服务运行状况”  。
3. 按照[创建有关服务通知的活动日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal)中的说明设置警报和通知。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何设置和使用 Azure 服务运行状况来监视 Windows 虚拟桌面的服务问题和运行状况公告。 若要了解如何登录到 Windows 虚拟桌面，请继续学习“连接到 Windows 虚拟桌面”操作指南。

> [!div class="nextstepaction"]
> [连接到 Windows 7 和 Windows 10 上的远程桌面客户端](./connect-windows-7-10.md)
