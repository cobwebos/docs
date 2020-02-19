---
title: Azure 实验室服务的防火墙设置
description: 了解如何在实验室中确定虚拟机的公共 IP 地址和端口号范围，以便可以将信息添加到防火墙规则。
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443443"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure 实验室服务的防火墙设置

每个组织或学校都将使用最适合其需求的方式设置自己的网络。  有时包括设置防火墙规则，阻止远程桌面协议（rdp）或安全外壳（ssh）连接到其自己的网络之外的计算机。  由于 Azure 实验室服务在公有云中运行，因此可能需要一些额外的配置，以允许学生从校园网络连接时访问其 VM。

每个实验室使用单个公共 IP 地址和多个端口。  所有 Vm （模板 VM 和学生 Vm）都将使用此公共 IP 地址。  公共 IP 地址在实验室的生存期内不会更改。  但是，每个 VM 都将具有不同的端口号。  端口号的范围为49152到65535。  公共 IP 地址和端口号的组合用于将教师和学生连接到正确的 VM。  本文介绍如何查找实验室使用的特定公共 IP 地址。  该信息可用于更新入站和出站防火墙规则，以便学生可以访问其 Vm。

>[!IMPORTANT]
>每个实验室都有不同的公共 IP 地址。

## <a name="find-public-ip-for-a-lab"></a>查找实验室的公共 IP

实验室服务实验室帐户的 "**所有实验室**" 页中列出了每个实验室的公共 IP 地址。  有关如何查找 "**所有实验室**" 页的说明，请参阅[如何在实验室帐户中管理实验室](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)。  

> [!div class="mx-imgBorder"]
> ![所有实验室页面](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>如果尚未发布实验室的模板计算机，则不会看到公共 IP 地址。

## <a name="conclusion"></a>结束语

现在，我们知道实验室的公共 IP 地址。  可以为组织的防火墙创建公共 ip 地址和端口范围49152-65535 的入站和出站规则。  更新规则后，学生可以访问其 Vm，而不会阻止访问网络防火墙。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [允许 lab creator 选取实验室位置](allow-lab-creator-pick-lab-location.md)
- [将实验室的网络连接到对等虚拟网络](how-to-connect-peer-virtual-network.md)
- [将共享映像库附加到实验室](how-to-attach-detach-shared-image-gallery.md)
- [添加用户作为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)
