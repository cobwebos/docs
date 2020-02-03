---
title: include 文件
description: include 文件
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 01/22/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 2e06a6c8dd7eb58769f504db9f96e0303c3e9f4c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748772"
---
下面是 Azure Active Directory (Azure AD) 服务的使用限制和其他服务限制。

| 类别 | 限制 |
| --- | --- |
| 目录 | 单个用户最多可以是 500 个 Azure AD 目录的成员或来宾。<br/>单个用户最多可以创建 20 个目录。 |
| 域 | 可以添加不超过 900 个的托管域名。 如果将所有域设置为与本地 Active Directory 联合，则可在每个目录中添加不超过 450 个域名。 |
|资源 |<ul><li>默认情况下，每个免费版 Azure Active Directory 的用户最多可以在单个目录中创建 50000 Azure AD 资源。 如果至少有一个已验证的域，Azure AD 中的默认目录服务配额将扩展为 300000 Azure AD 资源。 </li><li>非管理员用户最多可以创建250个 Azure AD 资源。 可用来还原的活动资源和已删除资源均计入此配额。 仅已删除的已删除 Azure AD 资源可用于还原。 删除 Azure AD 的资源，这些资源在30天的一个季度内，不能再还原到此配额的计数。 如果你的开发人员可能会在其常规职责的过程中反复超过此配额，则可以[创建并分配一个自定义角色，该角色](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md)有权创建无限数量的应用注册。</li></ul> |
| 架构扩展 |<ul><li>字符串类型扩展最多只能有 256 个字符。 </li><li>二进制类型扩展限制在 256 字节以内。</li><li>在*所有*类型和*所有*应用程序中，只能将100扩展值写入任何单个 Azure AD 资源。</li><li>仅“用户”、“组”、“TenantDetail”、“设备”、“应用程序”和“ServicePrincipal”实体可以用字符串类型或二进制类型单一值属性进行扩展。</li><li>架构扩展仅在 Graph API 1.21 预览版中可用。 必须授予应用程序编写访问注册扩展的权限。</li></ul> |
| 应用程序 |最多有 100 位用户可以是单一应用程序的所有者。 |
|应用程序清单 |最多可在应用程序清单中添加1200个条目。 |
| 组 |<ul><li>用户最多可在 Azure AD 组织中创建250组。</li><li>Azure AD 组织最多可以有5000个动态组。<li>最多有 100 位用户可以是单一组的所有者。</li><li>任意数量的 Azure AD 资源可以是单个组的成员。</li><li>一个用户可以是任意数量的组的成员。</li><li>使用 Azure AD Connect 时，一个小组中从本地 Active Directory 同步到 Azure Active Directory 的成员数目仅限 50,000。</li><li>并非在所有方案中都支持 Azure AD 中的嵌套组</li></ul><br/> 目前，以下是嵌套组支持的方案。<ul><li> 一个组可以添加为另一个组的成员，并且可以实现组嵌套。</li><li> 组成员身份声明（当应用配置为接收令牌中的组成员身份声明时，已登录用户是其成员的嵌套组）</li><li>条件性访问（在将条件访问策略限定为组时）</li><li>限制对自助密码重置的访问</li><li>限制哪些用户可以执行 Azure AD 联接和设备注册</li></ul><br/>以下方案不支持嵌套组：<ul><li> 应用角色分配（支持向应用分配组，但嵌套在直接分配组内的组将没有访问权限），可用于访问和预配</li><li>基于组的许可（将许可证自动分配给组的所有成员）</li><li>Office 365 组。</li></ul> |
| 应用程序代理 | <ul><li>每个应用代理应用程序最多每秒500个事务</li><li>Azure AD 组织最多每秒750个事务</li></ul><br/>事务定义为单一的 http 请求和对唯一资源的响应。 如果受到限制，客户端将收到429响应（请求过多）。 |
| 访问面板 |<ul><li>对于可以在访问面板中按用户查看的应用程序，其数量没有限制。 这适用于 Azure AD Premium 或企业移动性套件的用户分配许可证。</li><li>每位用户最多可以在访问面板中看到 10 个应用磁贴。 此限制适用于为 Azure AD Free 许可证计划分配许可证的用户。 Box、Salesforce、Dropbox 都是应用磁贴的示例。 此限制不适用于管理员帐户。</li></ul> |
| 报表 | 在报告中最多可查看或下载 1,000 行。 系统会截断其他任何数据。 |
| 管理单元 | Azure AD 资源可以是不超过30个管理单元的成员。 |
| 管理员角色和权限 | <ul><li>无法将组添加为[所有者](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)。</li><li>不能将组分配给[角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。</li><li>无法在 Azure AD 组织范围内切换用户读取其他用户的目录信息，从而禁止所有非管理员用户访问所有目录信息（不推荐）。 有关默认权限的详细信息，请参阅[此处](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)。</li><li>在管理员角色成员身份添加和吊销生效之前，可能最多需要15分钟或注销/登录。</li></ul> |
