---
title: 创建和管理 Azure AD 授权管理 （预览版）-Azure Active Directory 中的目录
description: 了解如何在 Azure Active Directory 权利管理 （预览版） 中创建新资源和访问包的容器。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541610"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>创建和管理 Azure AD 授权管理 （预览版） 中的目录

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 权利管理目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-a-catalog"></a>创建目录

目录是资源和访问包的容器。 如果想要相关的资源分组并访问包，创建一个编录。 无论谁创建该目录将成为第一个目录所有者。 目录所有者可以添加其他目录所有者。

**必备角色：** 用户管理员或目录的创建者

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击**目录**。

    ![在 Azure 门户中的权利管理目录](./media/entitlement-management-catalog-create/catalogs.png)

1. 单击**新的目录**。

1. 输入目录的唯一名称，并提供说明。

    用户将看到此信息访问包的详细信息中。

1. 如果你希望为可供用户请求，因为它们创建此目录中的访问包，设置**已启用**到**是**。

1. 如果你想要允许用户在所选外部目录，以便能够请求访问此目录中的包，请设置**为外部用户启用**到**是**。

    ![新的目录窗格](./media/entitlement-management-catalog-create/new-catalog.png)

1. 单击**创建**用于创建目录。

## <a name="add-resources-to-a-catalog"></a>将资源添加到目录

若要访问包中包含的资源，资源必须存在于目录。 可以添加的资源的类型是组、 应用程序和 SharePoint Online 网站。

**必备角色：** 用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击**目录**，然后打开你想要将资源添加到的目录。

1. 在左侧菜单中，单击**资源**。

1. 单击**将资源添加**。

1. 单击资源类型：**组**，**应用程序**，或**SharePoint 站点**。

    如果你是目录创建者，可以添加任何 Office 365 组或你拥有到目录的 Azure AD 安全组。 如果你想要分配给用户，一个组，但您不拥有组，需要有用户管理员将该组添加到你的目录。

    如果你是目录创建者，可以添加任何 Azure AD 企业应用程序，你拥有，包括 SaaS 应用程序和自己的应用程序联合到 Azure AD 中，为你的目录。 如果想要分配到用户，但不是属于您的应用程序，需要已添加到你的目录的应用程序的用户管理员。 应用程序目录的一部分后，你可以选择的任何应用程序的角色访问包中。

1. 选择你想要添加到目录中的类型的一个或多个资源。

1. 完成后，单击**添加**。

    现在可以在目录中访问包中包含这些资源。

## <a name="remove-resources-from-a-catalog"></a>从目录中删除资源

可以从目录中删除资源。 如果它未使用任何目录的访问包中，仅可以从目录中删除资源。

**必备角色：** 用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击**目录**，然后打开你想要删除的资源的目录。

1. 在左侧菜单中，单击**资源**。

1. 选择你想要删除的资源。

1. 单击**删除**(或单击省略号 ( **...** )，然后单击**删除资源**)。

## <a name="add-catalog-owners-or-access-package-managers"></a>添加目录所有者或访问包管理器

如果你想要委托管理的目录或访问包目录中的，你添加目录所有者，或访问包管理器。 任何人创建一个目录将成为第一个目录所有者。

**必备角色：** 用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击**目录**，然后打开你想要添加到管理员的目录。

1. 在左侧菜单中，单击**角色和管理员**。

1. 单击**添加所有者**或**访问包管理器添加**若要选择这些角色的成员。

1. 单击**选择**添加这些成员。

## <a name="edit-a-catalog"></a>编辑目录

您可以编辑的名称和目录的说明。 用户看到此信息访问包的详细信息中。

**必备角色：** 用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击**目录**，然后打开你想要编辑的目录。

1. 对目录**概述**页上，单击**编辑**。

1. 编辑目录的名称或说明。

1. 单击“ **保存**”。

## <a name="delete-a-catalog"></a>删除目录

如果它不具有任何访问包，可以删除一个目录，但仅。

**必备角色：** 用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中，单击**目录**，然后打开你想要删除的目录。

1. 对目录**概述**，单击**删除**。

1. 在出现的消息框中单击“是”  。

## <a name="next-steps"></a>后续步骤

- [创建和管理访问包](entitlement-management-access-package-create.md)
