---
title: Windows 虚拟桌面的数据位置-Azure
description: 对 Windows 虚拟机的数据和元数据的存储位置的简要概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b8cbee74a8f9639fc2c3838f38dabf2a62cf339e
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614442"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Windows 虚拟桌面的数据位置

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../data-locations.md)。

Windows 虚拟桌面目前可用于所有地理位置。 最初，只能将服务元数据存储在美国（US）地理位置。 管理员可以在创建主机池虚拟机和相关服务（如文件服务器）时选择存储用户数据的位置。 在[azure 数据中心地图](https://azuredatacentermap.azurewebsites.net/)上详细了解 azure 地理位置。

>[!NOTE]
>Microsoft 不会控制或限制你或你的用户可以访问你的用户和特定于应用程序的数据的区域。

>[!IMPORTANT]
>Windows 虚拟桌面将全局元数据信息存储在美国中的数据中心，例如租户名称、主机池名称、应用组名称和用户主体名称。 存储的元数据是静态加密的，并且异地冗余镜像在美国中进行维护。 所有客户数据（如应用设置和用户数据）驻留在客户选择的位置，并且不受服务管理。

出于灾难恢复目的，在美国中复制服务元数据。