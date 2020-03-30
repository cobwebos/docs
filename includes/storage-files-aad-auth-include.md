---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565073"
---
[Azure 文件](../articles/storage/files/storage-files-introduction.md)通过[活动目录 （AD） （预览）](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)和 Azure[活动目录域服务 （Azure AD DS） （GA）](../articles/active-directory-domain-services/overview.md)支持通过服务器消息块 （SMB） 进行基于身份的身份验证。 本文重点介绍 Azure 文件如何利用本地或 Azure 中的域服务来支持通过 SMB 对 Azure 文件的基于标识的访问。 这允许您轻松地将现有文件服务器替换为 Azure 文件，并继续使用现有目录服务，从而保持用户对共享的无缝访问。 

Azure 文件强制授权用户访问共享和目录/文件级别。 可以将共享级别权限分配分配给 Azure AD 用户或通过典型的[基于角色的访问控制 （RBAC）](../articles/role-based-access-control/overview.md)模型管理的组。 使用 RBAC 时，用于文件访问的凭据应可用或同步到 Azure AD。 您可以将内置 RBAC 角色（如存储文件数据 SMB 共享读取器）分配给 Azure AD 中的用户或组，以授予对 Azure 文件共享的读取访问权限。

在目录/文件级别，Azure 文件支持保留、继承和强制执行[Windows DACL，](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists)就像任何 Windows 文件服务器一样。 如果将数据通过 SMB 从文件共享复制到 Azure 文件，反之亦然，则可以选择保留 Windows DACL。 无论是否计划强制实施授权，都可以利用 Azure 文件与数据一起备份 ACL。 
