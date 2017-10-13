---
title: "Azure Active Directory 域服务：更新 Azure 虚拟网络的 DNS 设置 | Microsoft Docs"
description: "Azure Active Directory 域服务入门"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: ab8e3215e8e73d3943af06cffafa730cf1b7744b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-active-directory-domain-services"></a>启用 Azure Active Directory 域服务

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>任务 4：更新 Azure 虚拟网络的 DNS 设置
在前面的配置任务中，已成功为目录启用 Azure Active Directory 域服务。 下一个任务是确保虚拟网络中的计算机可以连接和使用这些服务。 在本文中，需要更新虚拟网络的 DNS 服务器设置，使其指向 Azure Active Directory 域服务在虚拟网络上可用的两个 IP 地址。

若要更新已启用 Azure Active Directory 域服务的虚拟网络的 DNS 服务器设置，请完成以下步骤：

1. “概览”选项卡列出了一组“必需的配置步骤”，这些步骤将在完全预配托管域后执行。 第一个配置步骤是“更新虚拟网络的 DNS 服务器设置”。

    ![完全预配后的“域服务 - 概览”选项卡](./media/getting-started/domain-services-provisioned-overview.png)

2. 完全预配域以后，此磁贴中会显示两个 IP 地址。 每个这样的 IP 地址都代表托管域的一个域控制器。

3. 若要将第一个 IP 地址复制到剪贴板，请单击其旁边的复制按钮。 然后单击“配置 DNS 服务器”按钮。

4. 将第一个 IP 地址粘贴到“DNS 服务器”边栏选项卡的“添加 DNS 服务器”文本框中。 水平滚动到左侧，复制第二个 IP 地址并将其粘贴到“添加 DNS 服务器”文本框中。

    ![域服务 - 更新 DNS](./media/getting-started/domain-services-update-dns.png)

5. 更新完虚拟网络的 DNS 服务器以后，单击“保存”。

> [!NOTE]
> 网络中的虚拟机必须重启才能获取新的 DNS 设置。 如果需立即获取更新的 DNS 设置，可通过门户、PowerShell 或 CLI 触发重启。
>
>

## <a name="next-step"></a>后续步骤
[任务 5：允许将密码同步到 Azure Active Directory 域服务](active-directory-ds-getting-started-password-sync.md)
