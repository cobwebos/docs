---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d1b10b55481b41f42c6c872522d3dd4dd4be0e77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570588"
---
[Azure 文件](../articles/storage/files/storage-files-introduction.md)通过服务器消息块 (SMB) (预览版) [Azure Active Directory 域服务 (AD DS)](../articles/active-directory-domain-services/overview.md)支持基于身份的身份验证。 已加入域的 Windows 虚拟机 (Vm) 可以使用[Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md)凭据来访问 Azure 文件共享。

你可以通过使用[基于角色的访问控制 (RBAC)](../articles/role-based-access-control/overview.md), 管理 Azure 文件共享级别的访问权限, 例如 Azure AD 中的用户或组。 你可以定义自定义 RBAC 角色, 其中包含用于访问 Azure 文件的常用权限集。 当你将自定义 RBAC 角色分配给 Azure AD 标识时，此标识会根据这些权限获取对 Azure 文件共享的访问权限。

在预览版推出期间，Azure 文件还支持对文件共享中的所有文件和目录保留、继承和强制实施 [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx)。 如果将文件共享中的数据复制到 Azure 文件（反之亦然），可指定维护 NTFS DACL。 通过这种方式, 你可以使用 Azure 文件来实现备份方案, 同时保留本地文件共享和云文件共享之间的 NTFS DACL。 

> [!NOTE]
> - 对于 Linux Vm, 不支持服务器消息块 (SMB) 访问 Azure AD DS 身份验证。 仅支持 Windows VM。
> - 对于 Active Directory 加入域的计算机, 不支持 SMB 访问 Azure AD DS 身份验证。 在这种情况下, 请考虑使用[Azure 文件同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)开始将数据迁移到 Azure 文件, 并通过使用本地 Active Directory 加入域的计算机上 Active Directory 凭据来继续强制执行访问控制。 
> - SMB 访问 Azure AD DS 身份验证仅适用于2018年9月24日之后创建的存储帐户。
> - Azure 文件同步管理的 Azure 文件共享上不支持 SMB 访问和 NTFS DACL 持久性 Azure AD DS 身份验证。
