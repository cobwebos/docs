---
title: 使用 Azure API 管理中的组管理开发人员帐户 | Microsoft 文档
description: 了解如何使用 Azure API 管理中的组管理开发人员帐户
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 3986b07c3568c3dcbb4077361d38f74d658458cd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>如何创建和使用组来管理 Azure API 管理中的开发人员帐户
在 API 管理中，使用组来管理产品对开发人员的可见性。 产品首次对组可见，这些组中的开发人员可以查看和订阅与组关联的产品。 

API 管理具有下列不可变的系统组：

* **管理员** - Azure 订阅管理员是此组的成员。 管理员管理 API 管理服务实例、创建 API、操作，以及开发人员所使用的产品。
* **开发人员** - 已经过身份验证的开发人员门户用户属于此组。 开发人员是使用 API 构建应用程序的客户。 开发人员有权访问开发人员门户，并构建调用 API 操作的应用程序。
* **来宾** - 未经身份验证的开发人员门户用户，如访问此组中 API 管理实例的开发人员门户的潜在客户。 它们可以被授予某些只读访问权限，如能够查看 API，但不能调用它们。

除了这些系统组，管理员还可以创建自定义组或[利用关联 Azure Active Directory 租户中的外部组][leverage external groups in associated Azure Active Directory tenants]。 自定义组和外部组可与系统组一起使用为开发人员提供可见性并可以访问 API 产品。 例如，可以为隶属于一个特定合作伙伴组织的开发人员创建一个自定义组并允许他们从仅包含相关 API 的产品中访问 API。 用户可以是多个组的成员。

本指南演示 API 管理实例的管理员如何添加新组并将它们关联产品和开发人员。

除了在发布者门户中创建和管理组，还可使用 API 管理 REST API [组](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)实体创建和管理组。

## <a name="prerequisites"></a>先决条件

完成此文中的任务：[创建 Azure API 管理实例](get-started-create-service-instance.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>创建组

本部分说明如何将新组添加到 API 管理帐户。

1. 选择屏幕左侧的“组”选项卡。
2. 单击“+添加”。
3. 输入组的唯一名称和可选说明。
4. 按“创建”。

    ![添加新组](./media/api-management-howto-create-groups/groups001.png)

创建组后，该组将添加到“组”列表。 <br/>若要编辑组的“名称”或“说明”，请单击组的名称和“设置”。<br/>若要删除组，请单击组的名称并按“删除”。

现在已创建组，它可以与产品和开发人员相关联。

## <a name="associate-group-product"> </a>将组与产品关联

1. 选择左侧的“产品”选项卡。
2. 单击所需产品的名称。
3. 按“访问控制”。
4. 单击“+ 添加组”。

    ![将组与产品关联](./media/api-management-howto-create-groups/groups002.png)
5. 选择要添加的组。

    ![将组与产品关联](./media/api-management-howto-create-groups/groups003.png)

    若要从产品中删除组，请单击“删除”。

    ![删除组](./media/api-management-howto-create-groups/groups004.png)

一种产品与组相关联后，该组中的开发人员可以查看和订阅该产品。

> [!NOTE]
> 若要添加 Azure Active Directory 组，请参阅[如何在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户](api-management-howto-aad.md)。

## <a name="associate-group-developer"> </a>将组与开发人员关联

本部分演示如何将组与成员相关联。

1. 选择屏幕左侧的“组”选项卡。
2. 选择“成员”。

    ![添加成员](./media/api-management-howto-create-groups/groups005.png)
3. 按“+添加”并选择成员。

    ![添加成员](./media/api-management-howto-create-groups/groups006.png)
4. 按“选择”。

一旦开发人员和组之间添加关联，可以在“用户”选项卡中查看它。

## <a name="next-steps"></a>后续步骤

* 一旦开发人员添加到组，他们可以查看和订阅与该组关联的产品。 有关详细信息，请参阅[如何在 Azure API 管理中创建和发布产品][How create and publish a product in Azure API Management]，
* 除了在发布者门户中创建和管理组，还可使用 API 管理 REST API [组](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity)实体创建和管理组。

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
