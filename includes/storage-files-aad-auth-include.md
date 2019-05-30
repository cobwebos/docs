---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/22/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 0cfa0fdb51969c92e767adfa86a0065d11da56e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237748"
---
[Azure 文件](../articles/storage/files/storage-files-introduction.md)使用 [Azure Active Directory (Azure AD) 域服务](../articles/active-directory-domain-services/overview.md)，支持通过 SMB（服务器消息块）实现的基于标识的身份验证（预览版）。 域加入 Windows 虚拟机 (VM) 可使用 [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md) 凭据访问 Azure 文件共享。 

Azure AD 使用[基于角色的访问控制 (RBAC)](../articles/role-based-access-control/overview.md) 验证标识（如用户、组或服务主体）。 可定义自定义 RBAC 角色，其中包含用于访问 Azure 文件的常用权限集。 当你将自定义 RBAC 角色分配给 Azure AD 标识时，此标识会根据这些权限获取对 Azure 文件共享的访问权限。

在预览版推出期间，Azure 文件还支持对文件共享中的所有文件和目录保留、继承和强制实施 [NTFS DACL](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx)。 如果将文件共享中的数据复制到 Azure 文件（反之亦然），可指定维护 NTFS DACL。 这样一来，可使用 Azure 文件实现备份方案，同时保留本地文件共享和云文件共享之间的 NTFS DACL。 

> [!NOTE]
> - Linux VM 不支持通过 SMB 实现的 Azure AD 身份验证（预览版）。 只有 Windows Server VM 才支持。
> - 访问 Azure 文件的本地计算机不支持通过 SMB 进行 Azure AD 身份验证。
> - Azure AD 身份验证仅适用于 2018 年 9 月 24 日后创建的存储帐户。
> - Azure 文件同步服务托管的 Azure 文件共享不支持通过 SMB 进行 Azure AD 身份验证和 NTFS ACL 持久性。 
