---
title: Azure VMware 解决方案（按云简单 - 升级云简单权限）
description: 描述如何升级云简单权限以在私有云 vCenter 中执行管理功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025327"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>升级云简单权限，以在私有云 vCenter 中执行管理功能

CloudSimple 权限方法旨在为 vCenter 用户提供执行正常操作所需的权限。 在某些情况下，用户可能需要其他权限才能执行特定任务。  您可以在有限时间内升级 vCenter SSO 用户的权限。

升级特权的原因可能包括：

* 标识源的配置
* 用户管理
* 删除分布式端口组
* 安装 vCenter 解决方案（如备份应用）
* 创建服务帐户

> [!WARNING]
> 在升级的特权状态下执行的操作可能会对系统产生负面影响，并可能导致系统不可用。 在上报期间仅执行必要的操作。

从云简单门户中，[在](escalate-private-cloud-privileges.md)vCenter SSO 上升级云所有者本地用户的权限。  仅当在 vCenter 上配置了其他标识提供程序时，才能提升远程用户的权限。  权限升级涉及将所选用户添加到 vSphere 内置管理员组。  只有一个用户可以具有升级的权限。  如果需要升级其他用户的权限，请首先取消升级当前用户的权限。

必须添加来自其他标识源的用户作为 CloudOwner 组的成员。

> [!CAUTION]
> 新用户只能添加到*云所有者组*、*云-全球群集-管理员组*、*云-全球-存储-管理员组*、*云-全球-网络-管理员组*或*云-全球-VM-管理员组*。  添加到*管理员*组的用户将自动删除。  只能将服务帐户添加到*管理员*组，并且不得使用服务帐户登录到 vSphere Web UI。

在升级期间，CloudSimple 使用自动监视与关联的警报通知来识别对环境的任何意外更改。
