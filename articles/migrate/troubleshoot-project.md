---
title: Azure Migrate 项目的疑难解答
description: 帮助您解决有关创建和管理 Azure Migrate 项目的问题。
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: 3b8c2f6ec33965317d2aaa23a36b6becff11a54a
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725723"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Azure Migrate 项目的疑难解答

本文将帮助你解决在创建和管理[Azure Migrate](migrate-services-overview.md)项目时遇到的问题。

## <a name="how-to-add-new-project"></a>如何添加新项目？

一个订阅中可以有多个 Azure Migrate 项目。 [了解如何](how-to-add-tool-first-time.md)首次创建项目，或[添加其他](create-manage-projects.md#create-additional-projects)项目。

## <a name="what-azure-permissions-are-needed"></a>需要哪些 Azure 权限？

你需要订阅中的 "参与者" 或 "所有者" 权限才能创建 Azure Migrate 项目。

## <a name="cant-find-a-project"></a>找不到项目

查找现有 Azure Migrate 项目取决于你使用的是当前版本还是旧版本的 Azure Migrate。 [跟随](create-manage-projects.md#find-a-project)。


## <a name="cant-find-a-geography"></a>找不到地理位置

可以在[支持的地理](migrate-support-matrix.md#supported-geographies)位置创建 Azure Migrate 项目。 请注意，"地理位置" 项目用于存储发现的计算机元数据。 也可以在其他位置评估或迁移计算机。

## <a name="what-are-vm-limits"></a>什么是 VM 限制？

可以在单个项目中评估最多35000个 VMware Vm 或最多35000个 Hyper-v Vm。 项目可以包括 VMware Vm 和 Hyper-v Vm，最高可达评估限制。

## <a name="can-i-upgrade-old-project"></a>能否升级旧项目？

无法更新以前版本的 Azure Migrate 中的项目。 你需要[创建一个新项目](how-to-add-tool-first-time.md)，并向其中添加工具。

## <a name="cant-create-a-project"></a>无法创建项目

如果尝试创建项目并遇到部署错误：

- 如果是暂时性错误，请尝试再次创建项目。 在**部署**中，单击 "**重新部署**" 重试。
- 检查订阅中是否有参与者或所有者权限。
- 如果要在新添加的地理位置进行部署，请稍等片刻，然后重试。
- 如果收到错误 "请求必须包含用户标识标头"，这可能表示无权访问组织的 Azure Active Directory （Azure AD）租户。 在这种情况下：
    - 当你首次添加到 Azure AD 租户时，你将收到加入租户的电子邮件邀请。
    - 接受要添加到租户的邀请。
    - 如果看不到此电子邮件，请与有权访问租户的用户联系，要求他们[重新发送邀请](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)。
    - 收到邀请电子邮件后，将其打开，然后选择接受邀请的链接。 然后，注销 Azure 门户并重新登录。 （刷新浏览器将不起作用。）然后，你可以开始创建迁移项目。

## <a name="how-do-i-delete-a-project"></a>如何实现删除项目

[按照这些说明](create-manage-projects.md#delete-a-project)操作来删除项目。 请注意，删除项目时，将删除项目中有关已发现的计算机的项目和元数据。

## <a name="added-tools-dont-show"></a>添加的工具不显示

请确保已选择正确的项目。 在 Azure Migrate 中心 >**服务器**"或"**数据库**"中，单击屏幕右上角的"**迁移项目**"旁边的"**更改**"。 选择正确的订阅和项目名称 > **"确定"** 。 该页应以所选项目的已添加工具进行刷新。

## <a name="next-steps"></a>后续步骤

将[评估](how-to-assess.md)或[迁移](how-to-migrate.md)工具添加到 Azure Migrate 项目。