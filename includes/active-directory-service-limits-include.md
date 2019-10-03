---
title: include 文件
description: include 文件
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: c58977798fabc120f3a8647774f575bc32515dcd
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219021"
---
下面是 Azure Active Directory (Azure AD) 服务的使用限制和其他服务限制。

| 类别 | 限制 |
| --- | --- |
| 目录 | 单个用户最多可以是 500 个 Azure AD 目录的成员或来宾。<br/>单个用户最多可以创建 20 个目录。 |
| 域 | 可以添加不超过 900 个的托管域名。 如果将所有域设置为与本地 Active Directory 联合，则可在每个目录中添加不超过 450 个域名。 |
|资源 |<ul><li>默认情况下，每个免费版 Azure Active Directory 的用户最多可以在单个目录中创建 50000 Azure AD 资源。 如果至少有一个已验证的域，Azure AD 中的默认目录服务配额将扩展为 300000 Azure AD 资源。 </li><li>非管理员用户最多可以创建250个 Azure AD 资源。 可用来还原的活动资源和已删除资源均计入此配额。 仅已删除的已删除 Azure AD 资源可用于还原。 删除 Azure AD 的资源，这些资源在30天的一个季度内，不能再还原到此配额的计数。 如果你的开发人员可能会在其常规职责的过程中反复超过此配额，则可以[创建并分配一个自定义角色，该角色](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md)有权创建无限数量的应用注册。</li></ul> |
| 架构扩展 |<ul><li>字符串类型扩展最多只能有 256 个字符。 </li><li>二进制类型扩展限制在 256 字节以内。</li><li>在*所有*类型和*所有*应用程序中，只能将100扩展值写入任何单个 Azure AD 资源。</li><li>仅“用户”、“组”、“TenantDetail”、“设备”、“应用程序”和“ServicePrincipal”实体可以用字符串类型或二进制类型单一值属性进行扩展。</li><li>架构扩展仅在 Graph API 1.21 预览版中可用。 必须授予应用程序编写访问注册扩展的权限。</li></ul> |
| 应用程序 |最多有 100 位用户可以是单一应用程序的所有者。 |
| 个组 |<ul><li>最多有 100 位用户可以是单一组的所有者。</li><li>任意数量的 Azure AD 资源可以是单个组的成员。</li><li>一个用户可以是任意数量的组的成员。</li><li>使用 Azure AD Connect 时，一个小组中从本地 Active Directory 同步到 Azure Active Directory 的成员数目仅限 50,000。</li></ul> |
| 应用程序代理 | <ul><li>每个应用代理应用程序最多每秒500个事务</li><li>租户最多每秒750个事务</li></ul><br/>事务定义为单一的 http 请求和对唯一资源的响应。 如果受到限制，客户端将收到429响应（请求过多）。 |
| 访问面板 |<ul><li>对于可以在访问面板中按用户查看的应用程序，其数量没有限制。 这适用于 Azure AD Premium 或企业移动性套件的用户分配许可证。</li><li>每位用户最多可以在访问面板中看到 10 个应用磁贴。 此限制适用于为 Azure AD Free 许可证计划分配许可证的用户。 Box、Salesforce、Dropbox 都是应用磁贴的示例。 此限制不适用于管理员帐户。</li></ul> |
| 报告 | 在报告中最多可查看或下载 1,000 行。 系统会截断其他任何数据。 |
| 管理单元 | Azure AD 资源可以是不超过30个管理单元的成员。 |
| 管理员角色和权限 | <ul><li>无法将组添加为[所有者](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)。</li><li>无法将组分配给[角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。</li><li>无法在租户范围交换机之外限制用户读取其他用户的目录信息的能力，以禁止所有非管理员用户访问所有目录信息（不推荐）。 有关默认权限的详细信息，请参阅[此处](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)。</li><li>在管理员角色成员身份添加和撤销生效之前，最多可能需要 15 分钟或注销/登录。</li></ul> |
