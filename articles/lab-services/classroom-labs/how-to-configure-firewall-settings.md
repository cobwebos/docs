---
title: Azure 实验室服务的防火墙设置
description: 了解如何在实验室中确定虚拟机的公共 IP 地址和端口号范围，以便可以将信息添加到防火墙规则。
author: emaher
ms.author: enewman
ms.date: 12/12/2019
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: da1614e4a3e02ed91ef2d3c59ac4eb3eac0dcc7c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692766"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure 实验室服务的防火墙设置

每个组织或学校都将使用最适合其需求的方式设置自己的网络。  有时包括设置防火墙规则，阻止远程桌面协议（rdp）或安全外壳（ssh）连接到其自己的网络之外的计算机。  由于 Azure 实验室服务在公有云中运行，因此可能需要一些额外的配置，以允许学生从校园网络连接时访问其 VM。

每个实验室使用单个公共 IP 地址和多个端口。  所有 Vm （模板 VM 和学生 Vm）都将使用此公共 IP 地址。  公共 IP 地址在实验室的生存期内不会更改。  但是，每个 VM 都将具有不同的端口号。  端口号的范围为49152到65535。  公共 IP 地址和端口号的组合用于将教师和学生连接到正确的 VM。  本文介绍如何查找实验室使用的特定公共 IP 地址。  该信息可用于更新入站和出站防火墙规则，以便学生可以访问其 Vm。

>[!IMPORTANT]
>每个实验室都有不同的公共 IP 地址。

## <a name="find-public-ip-for-a-lab"></a>查找实验室的公共 IP

实验室服务实验室帐户的 "**所有实验室**" 边栏选项卡中列出了每个实验室的公共 IP 地址。  有关如何查找 "**所有实验室**" 边栏选项卡的说明，请参阅[如何在实验室帐户中管理实验室](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)。  

> [!div class="mx-imgBorder"]
> ![所有实验室边栏选项卡](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>如果尚未发布实验室的模板计算机，则不会看到公共 IP 地址。

## <a name="conclusion"></a>结束语

现在，我们知道实验室的公共 IP 地址。  可以为组织的防火墙创建公共 ip 地址和端口范围49152-65535 的入站和出站规则。  更新规则后，学生可以访问其 Vm，而不会阻止访问网络防火墙。
