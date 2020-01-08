---
title: 在授权管理中创建 & 管理资源-Azure AD
description: 了解如何在 Azure Active Directory 的权利管理中创建新的资源容器和访问包。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24e82abd2c95bcbfdde843a6636a809bb3aeb70c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422645"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>在 Azure AD 的权利管理中创建和管理资源的目录

## <a name="create-a-catalog"></a>创建目录

目录是资源和访问包的容器。 需要对相关资源进行分组并访问包时，可以创建目录。 创建目录的任何人都将成为第一个目录所有者。 目录所有者可以添加其他目录所有者。

**必备角色：** 全局管理员、用户管理员或目录创建者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**目录**"。

    ![Azure 门户中的权利管理目录](./media/entitlement-management-catalog-create/catalogs.png)

1. 单击 "**新建目录**"。

1. 输入目录的唯一名称并提供说明。

    用户将在访问包的详细信息中看到此信息。

1. 如果希望用户在创建该目录时立即请求访问包，请将 "**启用**" 设置为 **"是"** 。

1. 如果希望允许选定外部目录中的用户在此目录中请求访问包，请将 "**为外部用户启用**" 设置为 **"是"** 。

    ![新建目录窗格](./media/entitlement-management-shared/new-catalog.png)

1. 单击 "**创建**" 以创建目录。

## <a name="add-resources-to-a-catalog"></a>将资源添加到目录

若要在访问包中包含资源，这些资源必须存在于目录中。 可以添加的资源类型包括组、应用程序和 SharePoint Online 站点。 这些组可以是云创建的 Office 365 组，也可以是云创建的 Azure AD 安全组。 应用程序可以是 Azure AD 企业应用程序，包括 SaaS 应用程序和联合到 Azure AD 的你自己的应用程序。 网站可以是 SharePoint Online 网站或 SharePoint Online 网站集。

**必备角色：** 查看[将资源添加到目录的所需角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**目录**"，然后打开要向其添加资源的目录。

1. 在左侧菜单中，单击 "**资源**"。

1. 单击 "**添加资源**"。

1. 单击资源类型：**组和团队**、**应用程序**或**SharePoint 站点**。

    如果看不到要添加的资源或无法添加资源，请确保具有所需的 Azure AD 目录角色和授权管理角色。 你可能需要具有所需角色的用户将资源添加到你的目录。 有关详细信息，请参阅[将资源添加到目录中所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

1. 选择要添加到目录中的类型的一个或多个资源。

    ![将资源添加到目录](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 完成后，单击 "**添加**"。

    现在可以将这些资源包含在目录内的 access 包中。

## <a name="remove-resources-from-a-catalog"></a>从目录中删除资源

可以从目录中删除资源。 如果某个资源未在任何目录的访问包中使用，则只能从目录中删除该资源。

**必备角色：** 查看[将资源添加到目录的所需角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**目录**"，然后打开要从中删除资源的目录。

1. 在左侧菜单中，单击 "**资源**"。

1. 选择要删除的资源。

1. 单击 "**删除**" （或单击省略号（ **...** ），然后单击 "**删除资源**"）。

## <a name="add-additional-catalog-owners"></a>添加其他目录所有者

创建目录的用户将成为第一个目录所有者。 若要委派管理目录，请将用户添加到目录所有者角色。 这有助于共享目录管理责任。 

按照以下步骤将用户分配到目录所有者角色：

**必备角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**目录**"，然后打开要向其添加管理员的目录。

1. 在左侧菜单中，单击 "**角色和管理员**"。

    ![目录角色和管理员](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 单击 "**添加所有者**"，选择这些角色的成员。

1. 单击 "**选择**" 添加这些成员。

## <a name="edit-a-catalog"></a>编辑目录

可以编辑目录的名称和描述。 用户在访问包的详细信息中看到此信息。

**必备角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**目录**"，然后打开要编辑的目录。

1. 在目录的 "**概述**" 页上，单击 "**编辑**"。

1. 编辑目录的 "名称"、"说明" 或 "已启用" 设置。

    ![编辑目录设置](./media/entitlement-management-shared/catalog-edit.png)

1. 单击“ **保存**”。

## <a name="delete-a-catalog"></a>删除目录

您可以删除某个目录，但前提是它不包含任何访问包。

**必备角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。

1. 在左侧菜单中，单击 "**目录**"，然后打开要删除的目录。

1. 在目录**概述**中，单击 "**删除**"。

1. 在出现的消息框中单击“是”。

## <a name="next-steps"></a>后续步骤

- [委托访问控制以访问包管理器](entitlement-management-delegate-managers.md)
