---
title: 帐户管理-Azure VMware 解决方案（AVS）门户
description: 介绍如何在 Azure VMware 解决方案（AVS）门户中管理帐户
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025361"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>在 Azure VMware 解决方案（AVS）门户中管理帐户

创建 AVS 服务时，它会在 AVS 上创建帐户。 该帐户与服务所在的 Azure 订阅相关联。 订阅中拥有 "所有者" 和 "参与者" 角色的所有用户都可以访问 AVS 门户。 在 "帐户" 页上可以找到与 AVS 服务关联的 Azure 订阅 ID 和租户 ID。

若要在 AVS 门户中管理帐户，请[访问门户](access-cloudsimple-portal.md)，并在侧边菜单中选择 "**帐户**"。

选择 "**摘要**" 以查看有关公司的 AVS 配置的信息。 显示云配置的当前容量，包括 AVS 私有云的数量、总存储、vSphere 群集配置、节点数和计算核心数。 如果当前配置不满足你的所有需求，则包含一个链接以购买其他节点。

## <a name="email-alerts"></a>电子邮件警报

你可以添加你想要通知的任何用户的电子邮件地址，以了解有关 AVS 私有云配置的更改。

1. 在 "**其他电子邮件警报**" 区域中，单击 "**新建**"。
2. 输入电子邮件地址。
3. 按 Return。  

若要删除条目，请单击 " **X**"。

## <a name="avs-operator-access"></a>AVS 操作员访问

操作员访问设置允许支持工程师登录到 AVS 门户，从而帮助你进行故障排除。 默认情况下，此设置处于启用状态。 支持工程师在登录到你的客户帐户后执行的所有操作都将记录下来，可供你在**活动** > **审核**页上查看。

单击 "**已启用 AVS 操作员访问**" 切换，打开或关闭访问权限。
