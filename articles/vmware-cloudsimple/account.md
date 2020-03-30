---
title: 帐户管理 - 通过云简单门户提供的 Azure VMware 解决方案
description: 介绍如何通过云简单门户管理 Azure VMware 解决方案上的帐户
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025361"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>按云简单门户管理 Azure VMware 解决方案上的帐户

当您创建云简单服务时，它会在 CloudSimple 上创建一个帐户。 该帐户与服务所在的 Azure 订阅相关联。 订阅中具有所有者和参与者角色的所有用户都有权访问 CloudSimple 门户。 与 CloudSimple 服务关联的 Azure 订阅 ID 和租户 ID 位于"帐户"页上。

要管理云简单门户中的帐户[，请访问门户](access-cloudsimple-portal.md)并选择侧菜单上的 **"帐户**"。

选择 **"摘要"** 以查看有关公司云简单配置的信息。 将显示云配置的当前容量，包括私有云数、总存储数、vSphere 群集配置、节点数和计算核心数。 如果当前配置不能满足所有需求，则包含一个链接以购买其他节点。

## <a name="email-alerts"></a>电子邮件警报

您可以添加要通知的任何人的电子邮件地址，了解私有云配置的更改。

1. 在 **"其他电子邮件警报**"区域中，单击"**添加新**"。
2. 输入电子邮件地址。
3. 按返回。  

要删除条目，请单击**X**。

## <a name="cloudsimple-operator-access"></a>云简单操作员访问

操作员访问设置允许 CloudSimple 允许您支持工程师登录到云简单门户，从而帮助您进行故障排除。  默认情况下启用该设置。 支持工程师在登录客户帐户时执行的所有操作都记录在**活动** > **审核**页面上，可供您查看。

单击**启用 CloudSimple 操作员访问**切换以打开或关闭访问。
