---
title: 排查 Azure Migrate 项目问题
description: 帮助您解决创建和管理 Azure 迁移项目的问题。
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: b1fc4bce988b13a9ff76fd961d524ce945876054
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535394"
---
# <a name="troubleshoot-azure-migrate-projects"></a>排查 Azure Migrate 项目问题

本文可帮助您在创建和管理[Azure 迁移](migrate-services-overview.md)项目时解决问题。

## <a name="how-to-add-new-project"></a>如何添加新项目？

订阅中可以有多个 Azure 迁移项目。 [了解如何](how-to-add-tool-first-time.md)首次创建项目或[添加其他](create-manage-projects.md#create-additional-projects)项目。

## <a name="what-azure-permissions-are-needed"></a>需要哪些 Azure 权限？

您需要订阅中的参与者或所有者权限才能创建 Azure 迁移项目。

## <a name="cant-find-a-project"></a>找不到项目

查找现有的 Azure 迁移项目取决于您使用的是当前版本还是旧版本的 Azure 迁移。 [按照](create-manage-projects.md#find-a-project)。


## <a name="cant-find-a-geography"></a>找不到地理

您可以在[公共和政府](migrate-support-matrix.md#supported-geographies-public-cloud)[云](migrate-support-matrix.md#supported-geographies-azure-government)受支持的地理位置中创建 Azure 迁移项目。

## <a name="what-are-vm-limits"></a>什么是 VM 限制？

您可以在单个项目中评估多达 35，000 个 VMware VM 或多达 35，000 台 Hyper-V VM。 项目可以同时包括 VMware VM 和超 V VM，但达到评估限制。

## <a name="can-i-upgrade-old-project"></a>我可以升级旧项目吗？

无法更新以前版本的 Azure 迁移的项目。 您需要[创建一个新项目](how-to-add-tool-first-time.md)，并添加工具。

## <a name="cant-create-a-project"></a>无法创建项目

如果尝试创建项目并遇到部署错误：

- 请尝试再次创建项目，以防出现暂时性错误。 在 **"部署"中**，单击 **"重新部署**"以重试。
- 检查订阅中具有参与者或所有者权限。
- 如果要在新添加的地理位置中部署，请稍等片刻，然后重试。
- 如果收到错误，"请求必须包含用户标识标头"，这可能表示您无法访问组织的 Azure 活动目录 （Azure AD） 租户。 在这种情况下：
    - 首次添加到 Azure AD 租户时，会收到加入租户的电子邮件邀请。
    - 接受要添加到租户的邀请。
    - 如果您看不到电子邮件，请与有权访问租户的用户联系，并要求他们[重新向您发送邀请](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)。
    - 收到邀请电子邮件后，打开它并选择接受邀请的链接。 然后，注销 Azure 门户，然后重新登录。 （刷新浏览器不起作用。然后，您可以开始创建迁移项目。

## <a name="how-do-i-delete-a-project"></a>如何删除项目

[按照这些说明](create-manage-projects.md#delete-a-project)删除项目。 请注意，删除项目时，将删除项目以及有关项目中发现的计算机的元数据。

## <a name="added-tools-dont-show"></a>添加的工具不显示

请确保您选择了正确的项目。 在 Azure 迁移中心>**服务器**或**数据库中**，单击屏幕右上角**的"迁移项目（更改）** 旁边**更改**"。 选择正确的订阅和项目名称>**确定**。 页面应使用所选项目的附加工具刷新。

## <a name="next-steps"></a>后续步骤

向 Azure 迁移项目添加[评估](how-to-assess.md)或[迁移](how-to-migrate.md)工具。