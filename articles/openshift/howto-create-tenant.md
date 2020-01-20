---
title: 创建适用于 Azure Red Hat OpenShift 的 Azure AD 租户
description: 下面介绍如何创建 Azure Active Directory （Azure AD）租户来托管你的 Microsoft Azure Red Hat OpenShift 群集。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76270449"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>创建适用于 Azure Red Hat OpenShift 的 Azure AD 租户

Microsoft Azure Red Hat OpenShift 需要一个用于创建群集的[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)租户。 *租户*是组织或应用开发人员通过注册 Azure、Microsoft Intune 或 Microsoft 365 与 Microsoft 建立关系时接收的 Azure AD 的专用实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户，并具有自己的工作、学校标识和应用注册。

如果还没有 Azure AD 租户，请按照以下说明创建一个。

## <a name="create-a-new-azure-ad-tenant"></a>创建新的 Azure AD 租户

创建租户：

1. 使用想要与 Azure Red Hat OpenShift 群集关联的帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 打开 " [Azure Active Directory" 边栏选项卡](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)以创建新的租户（也称为新的*Azure Active Directory*）。
3. 提供**组织名称**。
4. 提供一个**初始域名**。 这会在其中追加*onmicrosoft.com* 。 可在此处重复使用*组织名称*的值。
5. 选择将在其中创建租户的国家或地区。
6. 单击“创建”。
7. 创建 Azure AD 租户后，请选择 "**单击此处以管理新目录**" 链接。 新租户名称应显示在 Azure 门户的右上方：  

    ![显示右上方的租户名称的门户屏幕截图][tenantcallout]  

8. 记下*租户 ID* ，以便以后可以指定 Azure Red Hat OpenShift 群集的创建位置。 在门户中，现在应看到新租户的 "Azure Active Directory 概述" 边栏选项卡。 选择 "**属性**" 并复制**目录 ID**的值。 在[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程中，我们会将此值称为 `TENANT`。

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>资源

有关[Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)的详细信息，请查看[Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory/)。

## <a name="next-steps"></a>后续步骤

了解如何创建服务主体，如何生成客户端密钥和身份验证回调 URL，以及如何创建新的 Active Directory 用户来测试 Azure Red Hat OpenShift 群集上的应用。

[创建 Azure AD 应用对象和用户](howto-aad-app-configuration.md)
