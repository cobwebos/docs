---
title: Azure VMware 解决方案（AVS）-升级 AVS 权限
description: 描述如何升级 AVS 权限以在 AVS 私有云 vCenter 中执行管理功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 90dd61fc9856978bab0b68de19d48493a8e0c5fd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025327"
---
# <a name="escalate-avs-privileges-to-perform-administrative-functions-in-avs-private-cloud-vcenter"></a>升级 AVS 权限以在 AVS 私有云 vCenter 中执行管理功能

AVS 权限方法旨在为 vCenter 用户授予执行正常操作所需的特权。 在某些情况下，用户可能需要额外的权限来执行特定任务。 在有限的时间段内，你可以提升 vCenter SSO 用户的权限。

提升权限的原因包括：

* 标识源的配置
* 用户管理
* 删除分布式端口组
* 安装 vCenter 解决方案（例如备份应用）
* 创建服务帐户

> [!WARNING]
> 在升级特权状态中执行的操作可能会对系统产生负面影响，并可能导致系统变得不可用。 在升级期间仅执行必要的操作。

从 AVS 门户升级 vCenter SSO 上 CloudOwner 本地用户的[权限](escalate-private-cloud-privileges.md)。 仅当在 vCenter 上配置了其他标识提供程序时，才能提升远程用户的权限。 权限的升级涉及将选定的用户添加到 vSphere 内置管理员组。 只有一个用户可以具有提升的权限。 如果需要升级其他用户的权限，请先取消当前用户的权限。

必须将来自其他标识源的用户添加为 CloudOwner 组的成员。

> [!CAUTION]
> 新用户必须仅添加到*云所有者组*、*云全局-群集管理组*、云全局*存储管理*组、云全局*网络管理*组或*云-全局-VM 管理*组的用户组。  添加到*管理员*组的用户将被自动删除。  只有服务帐户必须添加到*Administrators*组，并且服务帐户不得用于登录 VSPHERE web UI。
在升级期间，AVS 使用带有相关警报通知的自动监视来识别对环境进行的任何意外更改。
