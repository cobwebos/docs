---
title: 为 Azure 红帽打开 Shift 创建 Azure AD 租户
description: 下面了解如何创建 Azure 活动目录 （Azure AD） 租户以承载 Microsoft Azure 红帽 OpenShift 群集。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243687"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>为 Azure 红帽打开 Shift 创建 Azure AD 租户

Microsoft Azure 红帽 OpenShift 需要[Azure 活动目录 （Azure AD）](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)租户才能在其中创建群集。 *租户*是 Azure AD 的专用实例，组织或应用开发人员在通过注册 Azure、Microsoft Intune 或 Microsoft 365 来创建与 Microsoft 的关系时收到该实例。 每个 Azure AD 租户都与其他 Azure AD 租户不同且不同，并且有自己的工作、学校标识和应用注册。

如果还没有 Azure AD 租户，请按照这些说明创建一个。

## <a name="create-a-new-azure-ad-tenant"></a>创建新的 Azure AD 租户

要创建租户：

1. 使用要与 Azure 红帽 OpenShift 群集关联的帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 打开[Azure 活动目录边栏选项卡](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)以创建新租户（也称为新的*Azure 活动目录*）。
3. 提供**组织名称**。
4. 提供**初始域名**。 这将*onmicrosoft.com*附加在它。 您可以在此处重用*组织名称*的值。
5. 选择将创建租户的国家或地区。
6. 单击 **“创建”**。
7. 创建 Azure AD 租户后，选择 **"单击此处"以管理新的目录**链接。 新租户名称应显示在 Azure 门户的右上角：  

    ![显示右上角租户名称的门户屏幕截图][tenantcallout]  

8. 记下*租户 ID，* 以便以后可以指定创建 Azure 红帽 OpenShift 群集的位置。 在门户中，现在应看到新租户的 Azure 活动目录概述边栏选项卡。 选择**属性**并复制**目录 ID**的值。 我们将引用此值，如`TENANT`创建 Azure[红帽 OpenShift 群集](tutorial-create-cluster.md)教程。

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>资源

有关[Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)的详细信息，请查看 Azure[活动目录文档](https://docs.microsoft.com/azure/active-directory/)。

## <a name="next-steps"></a>后续步骤

了解如何创建服务主体、生成客户端机密和身份验证回调 URL，以及创建新的 Active Directory 用户以测试 Azure 红帽 OpenShift 群集上的应用。

[创建 Azure AD 应用对象和用户](howto-aad-app-configuration.md)
