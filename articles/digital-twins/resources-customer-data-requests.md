---
title: Azure 数字孪生的客户数据请求功能
titleSuffix: Azure Digital Twins
description: 本文介绍了如何在 Azure 数字孪生中导出和删除个人数据。
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9822044de53f5f74df302e05e0e6c5cd06f3eb92
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737149"
---
# <a name="summary-of-customer-data-request-features"></a>客户数据请求功能摘要

Azure 数字孪生是一个开发人员平台，用于创建安全的业务环境数字表示形式。 表示形式由用户选择的数据源中的实时状态数据驱动。

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Azure 数字孪生中名为*数字孪生*的数字表示形式表示真实环境中的实体，并与标识符相关联。 Microsoft 不维护任何信息，也不能访问允许标识符与用户关联的数据。 

Azure 数字孪生中的许多数字孪生不会直接表示个人实体-表示的典型对象可能是办公室会议室或工厂地面。 但是，用户可能会认为某些实体是个人身份，并且他们可以自行决定如何维护自己的资产或库存跟踪方法，将数字孪生与个人联系在一起。 Azure 数字孪生管理和存储与数字孪生相关联的所有数据，就像它是个人数据一样。

若要查看、导出和删除数据主体请求中可能引用的个人数据，Azure 数字孪生管理员可以使用用户和角色的[**Azure 门户**](https://portal.azure.com/)，或使用[**AZURE 数字孪生 REST api**](how-to-use-apis-sdks.md) for 数字孪生。 Azure 门户和 REST Api 为用户提供了不同的方法来处理此类数据主体请求。

## <a name="identifying-customer-data"></a>标识客户数据

Azure 数字孪生将*个人数据*视为与其管理员和用户关联的数据。 

Azure 数字孪生存储具有环境访问权限的用户的[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) *对象 ID* 。 Azure 门户中的 Azure 数字孪生显示用户电子邮件地址，但这些电子邮件地址不会存储在 Azure 数字孪生中。 使用 Azure Active Directory 对象 ID 在 Azure Active Directory 中动态查找它们。

## <a name="deleting-customer-data"></a>删除客户数据

Azure 数字孪生管理员可以使用 Azure 门户删除与用户相关的数据。 还可以使用 Azure 数字孪生 REST Api 在单个数字孪生上执行删除操作。 有关可用 Api 的详细信息，请参阅[Azure 数字孪生 REST api 文档](https://docs.microsoft.com/rest/api/azure-digitaltwins/)。

## <a name="exporting-customer-data"></a>导出客户数据

Azure 数字孪生存储与数字孪生相关的数据。 用户可以通过 REST Api 检索和查看此数据，并使用复制和粘贴导出此数据。 

可以从 Azure 门户中选择、复制和粘贴客户数据（包括用户角色和角色分配）。 

## <a name="links-to-additional-documentation"></a>其他文档的链接

有关 Azure 数字孪生服务 Api 的完整列表，请参阅[Azure 数字孪生 REST api 文档](https://docs.microsoft.com/rest/api/azure-digitaltwins/)。