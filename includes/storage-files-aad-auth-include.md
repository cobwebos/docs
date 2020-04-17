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
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536569"
---
[Azure 文件](../articles/storage/files/storage-files-introduction.md)通过[本地活动目录域服务 （AD DS）](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) （预览） 和[Azure 活动目录域服务 （Azure AD DS）](../articles/active-directory-domain-services/overview.md)支持通过服务器消息块 （SMB） 进行基于身份的身份验证。 本文重点介绍 Azure 文件共享如何使用本地或 Azure 中的域服务来支持通过 SMB 对 Azure 文件共享的基于身份的访问。 为 Azure 文件共享启用基于标识的访问允许您使用 Azure 文件共享替换现有文件服务器，而无需替换现有目录服务，从而保持用户对共享的无缝访问。 

Azure 文件强制授权用户访问共享和目录/文件级别。 可以在 Azure 活动目录 （Azure AD） 用户或通过[基于角色的访问控制 （RBAC）](../articles/role-based-access-control/overview.md)模型管理的组上执行共享级权限分配。 使用 RBAC 时，用于文件访问的凭据应可用或同步到 Azure AD。 您可以将内置 RBAC 角色（如存储文件数据 SMB 共享读取器）分配给 Azure AD 中的用户或组，以授予对 Azure 文件共享的读取访问权限。

在目录/文件级别，Azure 文件支持保留、继承和强制执行[Windows DACL，](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists)就像任何 Windows 文件服务器一样。 在现有文件共享和 Azure 文件共享之间通过 SMB 复制数据时，可以选择保留 Windows DACL。 无论是否计划强制实施授权，都可以使用 Azure 文件共享备份 ACL 以及数据。 
