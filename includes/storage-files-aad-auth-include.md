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
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565073"
---
[Azure 文件](../articles/storage/files/storage-files-introduction.md)支持通过服务器消息块（SMB）进行基于身份的身份验证，通过[Active Directory （AD）](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) （预览版）和[AZURE ACTIVE DIRECTORY 域服务（Azure AD DS）](../articles/active-directory-domain-services/overview.md) （GA）。 本文重点介绍 Azure 文件如何利用本地或 Azure 中的域服务来支持基于 SMB 的 Azure 文件的基于标识的访问。 这样，你就可以轻松地将现有文件服务器替换为 Azure 文件，并继续使用现有的目录服务，从而无缝用户访问共享。 

Azure 文件对用户的授权强制实施对共享和目录/文件级别的访问权限。 可以通过典型的[基于角色的访问控制（RBAC）](../articles/role-based-access-control/overview.md)模型将共享级权限分配分配给 Azure AD 的用户或组。 使用 RBAC 时，用于文件访问的凭据应该可用或同步到 Azure AD。 可以向 Azure AD 中的用户或组分配内置 RBAC 角色（例如存储文件数据 SMB 共享读取器），以授予对 Azure 文件共享的读取权限。

在目录/文件级别，Azure 文件支持保留、继承和强制实施[Windows dacl](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) ，就像任何 windows 文件服务器一样。 如果通过 SMB 将数据从文件共享复制到 Azure 文件，或反之亦然，可以选择保留 Windows Dacl。 无论你是否打算强制实施授权，都可以利用 Azure 文件来备份 Acl 和数据。 
