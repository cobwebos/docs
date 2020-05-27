---
title: Azure 实验室服务的防火墙设置
description: 了解如何确定实验室中虚拟机的公共 IP 地址和端口号范围，以便将信息添加到防火墙规则中。
author: emaher
ms.author: enewman
ms.date: 05/15/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: dcb8c0d5304f052dbe7f14386425ce67ae3b79a6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589527"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure 实验室服务的防火墙设置

每个组织或学校都将以最适合其需求的方式建立自己的网络。  有时这包括设置防火墙规则，以阻止远程桌面协议 (rdp) 或安全外壳 (ssh) 连接到其自己网络之外的计算机。  由于 Azure 实验室服务在公有云中运行，因此可能需要一些额外配置，以允许学生从校园网连接时访问其 VM。

每个实验室均使用单个公共 IP 地址和多个端口。  所有 VM（模板 VM 和学生 VM）都将使用此公共 IP 地址。  此公共 IP 地址在实验室的生存期内不会更改。  但是，每个 VM 都会有不同的端口号。  端口号的范围为 49152 到 65535。  使用此公共 IP 地址和端口号的组合将讲师和学生连接到正确的 VM。  本文介绍如何查找实验室使用的特定公共 IP 地址。  可使用该信息更新入站和出站防火墙规则，以便学生访问其 VM。

>[!IMPORTANT]
>每个实验室都会有不同的公共 IP 地址。

## <a name="find-public-ip-for-a-lab"></a>查找实验室的公共 IP

每个实验室的公共 IP 地址都列在实验室服务实验室帐户的“所有实验室”页中。  有关如何查找“所有实验室”页的说明，请参阅[查看实验室帐户中的实验室](manage-labs.md#view-labs-in-a-lab-account)。  

> [!div class="mx-imgBorder"]
> ![“所有实验室”页](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>如果实验室的模板计算机尚未发布，则将看不到公共 IP 地址。

## <a name="conclusion"></a>结束语

现在我们已知道实验室的公共 IP 地址。  可以为公共 IP 地址和端口范围 49152-65535 创建组织防火墙的入站和出站规则。  更新规则后，学生便可以访问其 VM，而网络防火墙不会阻止访问。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [允许实验室创建者选取实验室位置](allow-lab-creator-pick-lab-location.md)
- [将实验室的网络连接到对等虚拟网络](how-to-connect-peer-virtual-network.md)
- [将共享映像库连接到实验室](how-to-attach-detach-shared-image-gallery.md)
- [添加用户作为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)
