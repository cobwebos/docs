---
title: Azure Active Directory 域服务：更新 Azure 虚拟网络的 DNS 设置 | Microsoft Docs
description: Azure Active Directory 域服务入门
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: d4ff41e51622adb7776df5e053025911bfa3ee16
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215403"
---
# <a name="enable-azure-active-directory-domain-services"></a>启用 Azure Active Directory 域服务

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>任务 4：更新 Azure 虚拟网络的 DNS 设置
在前面的配置任务中，已成功为目录启用 Azure Active Directory 域服务。 接下来，使虚拟网络中的计算机可以连接和使用这些服务。 在本文中，需要更新虚拟网络的 DNS 服务器设置，使其指向 Azure Active Directory 域服务在虚拟网络上可用的两个 IP 地址。

若要更新已启用 Azure Active Directory 域服务的虚拟网络的 DNS 服务器设置，请完成以下步骤：


1. “概览”选项卡列出了一组“必需的配置步骤”，这些步骤将在完全预配托管域后执行。 第一个配置步骤是“更新虚拟网络的 DNS 服务器设置”。

    ![域服务 -“概述”选项卡](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > 未看到此配置步骤？ 如果虚拟网络的 DNS 服务器设置是最新的，则在“概述”选项卡上将看不到“更新虚拟网络的 DNS 服务器设置”磁贴。
    >
    >

2. 单击“配置”按钮以更新虚拟网络的 DNS 服务器设置。

> [!NOTE]
> 网络中的虚拟机必须重启才能获取新的 DNS 设置。 如果需立即获取更新的 DNS 设置，可通过门户、PowerShell 或 CLI 触发重启。
>
>

## <a name="next-step"></a>后续步骤
[任务 5：允许将密码哈希同步到 Azure Active Directory 域服务](active-directory-ds-getting-started-password-sync.md)
