---
title: Azure 实验室服务的防火墙设置
description: 了解如何确定实验室中虚拟机的公共 IP 地址和端口号范围，以便将信息添加到防火墙规则中。
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443443"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure 实验室服务的防火墙设置

每个组织或学校都将以最适合其需求的方式建立自己的网络。  有时，这包括设置防火墙规则，以阻止远程桌面协议 （rdp） 或安全外壳 （ssh） 连接到其网络外部的计算机。  由于 Azure 实验室服务在公共云中运行，因此可能需要一些额外的配置，以允许学生在从园区网络连接时访问其 VM。

每个实验室使用单个公共 IP 地址和多个端口。  所有 VM（模板 VM 和学生 VM）都将使用此公共 IP 地址。  公共 IP 地址在实验室生命周期内不会更改。  但是，每个 VM 将具有不同的端口号。  端口号范围为 49152 到 65535。  公共 IP 地址和端口号的组合用于将教师和学生连接到正确的 VM。  本文将介绍如何查找实验室使用的特定公共 IP 地址。  这些信息可用于更新入站和出站防火墙规则，以便学生可以访问其 VM。

>[!IMPORTANT]
>每个实验室将具有不同的公共 IP 地址。

## <a name="find-public-ip-for-a-lab"></a>查找实验室的公共 IP

每个实验室的公共 IP 地址列在实验室服务实验室帐户的 **"所有实验室**"页面。  有关如何查找 **"所有实验室"** 页面的说明，请参阅[如何管理实验室帐户中的实验室](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)。  

> [!div class="mx-imgBorder"]
> ![所有实验室页面](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>如果实验室的模板计算机尚未发布，则看不到公共 IP 地址。

## <a name="conclusion"></a>结束语

现在我们知道实验室的公共 IP 地址。  可以为组织的公共 ip 地址和端口范围 49152-65535 创建入站和出站规则。  更新规则后，学生无需网络防火墙阻止访问即可访问其 VM。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [允许实验室创建者选取实验室位置](allow-lab-creator-pick-lab-location.md)
- [将实验室网络与对等虚拟网络连接](how-to-connect-peer-virtual-network.md)
- [将共享图像库附加到实验室](how-to-attach-detach-shared-image-gallery.md)
- [将用户添加为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)
