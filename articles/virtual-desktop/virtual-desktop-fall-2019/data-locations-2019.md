---
title: Windows 虚拟桌面 (经典) 的数据位置-Azure
description: 在中存储 Windows 虚拟桌面 (经典) 数据和元数据的位置的简要概述。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008808"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Windows 虚拟桌面 (经典) 的数据位置

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../data-locations.md)。

Windows 虚拟桌面目前可用于所有地理位置。 最初，只能将服务元数据存储在美国 (US) 地理位置。 管理员可以在创建主机池虚拟机和相关服务（如文件服务器）时选择存储用户数据的位置。 在 [azure 数据中心地图](https://azuredatacentermap.azurewebsites.net/)上详细了解 azure 地理位置。

>[!NOTE]
>Microsoft 不会控制或限制你或你的用户可以访问你的用户和特定于应用程序的数据的区域。

>[!IMPORTANT]
>Windows 虚拟桌面将全局元数据信息存储在美国中的数据中心，例如租户名称、主机池名称、应用组名称和用户主体名称。 存储的元数据是静态加密的，并且异地冗余镜像在美国中进行维护。 所有客户数据（如应用设置和用户数据）驻留在客户选择的位置，并且不受服务管理。

出于灾难恢复目的，在美国中复制服务元数据。