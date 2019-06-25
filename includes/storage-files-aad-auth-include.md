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
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269353"
---
[Azure 文件](../articles/storage/files/storage-files-introduction.md)使用 [Azure Active Directory (Azure AD) 域服务](../articles/active-directory-domain-services/overview.md)，支持通过 SMB（服务器消息块）实现的基于标识的身份验证（预览版）。 域加入 Windows 虚拟机 (VM) 可使用 [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) 凭据访问 Azure 文件共享。 

你可以管理 Azure 文件共享级别访问权限的用户，与 Azure AD 中的组等的标识[基于角色的访问控制 (RBAC)](../articles/role-based-access-control/overview.md)。 可定义自定义 RBAC 角色，其中包含用于访问 Azure 文件的常用权限集。 当你将自定义 RBAC 角色分配给 Azure AD 标识时，此标识会根据这些权限获取对 Azure 文件共享的访问权限。

在预览版推出期间，Azure 文件还支持对文件共享中的所有文件和目录保留、继承和强制实施 [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx)。 如果将文件共享中的数据复制到 Azure 文件（反之亦然），可指定维护 NTFS DACL。 这样一来，可使用 Azure 文件实现备份方案，同时保留本地文件共享和云文件共享之间的 NTFS DACL。 

> [!NOTE]
> - 适用于 Linux Vm 不支持的 SMB 访问权限的 azure AD 域服务身份验证。 仅支持 Windows VM。
> - Active Directory 加入域的计算机不支持的 SMB 访问权限的 azure AD 域服务身份验证。 在此期间，可以考虑利用[Azure 文件同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)开始将你的数据迁移到 Azure 文件并继续强制实施访问控制使用 AD 凭据从你的本地 AD 域已加入的计算机。 
> - 仅适用于 2018 年 9 月 24 日之后创建的存储帐户的 SMB 访问权限的 azure AD 域服务身份验证。
> - 在 Azure 文件同步服务管理的 Azure 文件共享上不支持的 SMB 访问权限和 NTFS DACL 持久的 azure AD 域服务身份验证。 
