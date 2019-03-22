---
title: Microsoft Azure 数据框边缘设备的访问、 电源和连接模式 |Microsoft Docs
description: 介绍如何管理访问、 电源和连接模式可帮助将数据传输到 Azure 的 Azure 数据框边缘设备
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: e0aa3468bda9f904d62e9e20545ac5f990cef521
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "57905282"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge-preview"></a>在 Azure 数据框 edge （预览版） 管理访问、 电源和连接模式

本文介绍如何在 Azure 数据框边缘为管理访问、 电源和连接模式。 这些操作是通过本地 Web UI 或 Azure 门户执行的。

在本文中，学习如何：

> [!div class="checklist"]
> * 管理对设备的访问
> * 管理连接模式
> * 管理电源

> [!IMPORTANT]
> Data Box Edge 以预览版提供。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="manage-device-access"></a>管理对设备的访问

向你的数据框边缘设备的访问控制通过使用设备管理员密码。 可以通过本地 Web UI 更改管理员密码。 还可以在 Azure 门户中重置设备管理员密码。

### <a name="change-device-administrator-password"></a>更改设备管理员密码

在本地 UI 中遵循以下步骤更改设备管理员密码。

1. 在本地 Web UI 中，转到“维护”>“密码更改”。
2. 依次输入当前密码和新密码。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 确认新密码。

    ![更改密码](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. 选择“更改密码”。
 
### <a name="reset-device-administrator-password"></a>重置设备管理员密码

重置工作流不需要用户回忆旧密码，在密码丢失时非常有用。 在 Azure 门户中执行此工作流。

1. 在 Azure 门户中，转到“概述”>“重置管理员密码”。

    ![重置密码](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. 输入新密码并确认。 提供的密码必须是 8 到 16 个字符。 该密码必须包含以下字符中的 3 项：大写字母、小写字母、数字和特殊字符。 选择**重置**。

    ![重置密码](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>管理连接模式

除了默认完全连接模式下，还可以在部分连接，或完全断开连接模式下运行你的设备。 下面介绍上述每种模式：

- **完全连接**-这是正常的默认模式在设备上进行操作。 在此模式下启用的云上传和下载数据。 可以使用 Azure 门户或本地 web UI 来管理设备。

- **部分离线** – 在此模式下，设备无法将任何数据上传到共享，但可以通过 Azure 门户管理设备。

    使用按流量计费的卫星网络，并且目标是尽量减少网络带宽消耗时，通常会使用此模式。 执行设备监视操作时，仍有可能会消耗少量的网络带宽。

- **离线** – 在此模式下，设备完全与云断开连接，此时会禁用云上传和下载。 只能通过本地 Web UI 管理设备。

    想要使设备脱机时，通常会使用此模式。

若要更改设备模式，请执行以下步骤：

1. 在设备的本地 Web UI 中，转到“配置”>“云设置”。
2. 从下拉列表中，选择你想要运行中的设备的模式。 可以选择从**完全连接**，**已部分连接**，并**完全断开连接**。 若要在部分离线模式下运行设备，请启用“Azure 门户管理”。

    ![连接模式](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>管理电源

可以关闭或重启物理设备使用本地 web UI。 我们建议您重新启动之前，使共享脱机数据服务器，然后在设备上的。 此操作可以最大程度地减少发生数据损坏的可能性。

1. 在本地 Web UI 中，转到“维护”>“电源设置”。
2. 选择**关机**或**重新启动**具体取决于你想要执行操作。

    ![电源设置](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. 当系统提示确认，则选择**是**以继续。

> [!NOTE]
> 如果你关闭物理设备，需要将其打开设备上按电源按钮。

## <a name="next-steps"></a>后续步骤

- 了解如何[管理共享](data-box-edge-manage-shares.md)。