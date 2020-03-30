---
title: Windows 虚拟桌面的数据位置 - Azure
description: Windows 虚拟桌面的数据和元数据存储在哪些位置的简要概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128058"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Windows 虚拟桌面的数据位置

Windows 虚拟桌面当前适用于所有地理位置。 最初，服务元数据只能存储在美国 （美国）地理中。 管理员可以选择在创建主机池虚拟机和相关服务（如文件服务器）时存储用户数据的位置。 在[Azure 数据中心映射](https://azuredatacentermap.azurewebsites.net/)中了解有关 Azure 地理位置的更多详细信息。

>[!NOTE]
>Microsoft 不控制或限制您或您的用户可以访问用户和应用特定数据的区域。

>[!IMPORTANT]
>Windows 虚拟桌面将全局元数据信息（如租户名称、主机池名称、应用组名称和用户主体名称）存储在位于美国的数据中心中。 存储的元数据在静态时加密，并且地理冗余镜像在美国境内维护。 所有客户数据（如应用设置和用户数据）都驻留在客户选择的位置，并且不受服务管理。

出于灾难恢复目的，服务元数据将在美国复制。