---
title: include 文件
description: include 文件
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 09/10/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 49d0d3e8665e0e80b1626c180300611149eff23f
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "90005968"
---
下面是 Azure Active Directory (Azure AD) 服务的使用限制和其他服务限制。

| 类别 | 限制 |
| --- | --- |
| 租户 | 单个用户最多可以是 500 个 Azure AD 租户的成员或来宾。<br/>单个用户最多可以创建 200 个目录。 |
| 域 | 可以添加不超过 900 个的托管域名。 如果将所有域设置为与本地 Active Directory 联合，则可在每个租户中添加不超过 450 个域名。 |
|资源 |<ul><li>默认情况下，Azure Active Directory 免费版用户最多可以在单个租户中创建 50,000 个 Azure AD 资源。 如果你有至少一个经过验证的域，则组织的默认默认 Azure AD 服务配额会扩展到 300000 个 Azure AD 资源。 此服务限制与 Azure AD 定价页上 500000 个资源的定价层限制无关。 若要超过默认配额，必须联系 Microsoft 支持部门。</li><li>非管理员用户最多可以创建 250 个 Azure AD 资源。 活动资源和可还原的已删除资源都会计入此配额。 只能还原在不到 30 天前删除的 Azure AD 资源。 不再可还原的已删除 Azure AD 资源在 30 天内按四分之一的值计入此配额。 如果开发人员在其日常工作期间可能会反复超过此配额，你可以[创建并分配一个自定义角色](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md)，并为此角色授予创建无限个应用注册的权限。</li></ul> |
| 架构扩展 |<ul><li>String 类型扩展最多只能有 256 个字符。 </li><li>Binary 类型扩展限制在 256 字节以内。</li><li>只能将 100 个扩展值（包括所有类型和所有应用程序）写入任何单一 Azure AD 资源中。</li><li>仅“用户”、“组”、“TenantDetail”、“设备”、“应用程序”和“ServicePrincipal”实体可以用字符串类型或二进制文件类型单一值属性进行扩展。</li><li>架构扩展仅在 Graph API 1.21 预览版中可用。 必须授予应用程序编写访问注册扩展的权限。</li></ul> |
| 应用程序 |最多有 100 位用户可以是单一应用程序的所有者。 |
|应用程序清单 |最多可在应用程序清单中添加 1200 个条目。 |
| 组 |<ul><li>一个非管理员用户最多可在 Azure AD 组织中创建 250 个组。 任何可以管理组织中的组的 Azure AD 管理员可以创建无限数量的组（最多可达 Azure AD 对象限额）。 如果指定了一个角色来删除用户的限制，请向其分配权限较低的内置角色，如“用户管理员”或“组管理员”。</li><li>一个 Azure AD 组织最多可以有 5000 个动态组。<li>最多有 100 位用户可以是单一组的所有者。</li><li>任意数量的 Azure AD 资源都可以是单个组的成员。</li><li>一个用户可以是任意数量的组的成员。</li><li>默认情况下，使用 Azure AD Connect 时，一个组中可以从本地 Active Directory 同步到 Azure Active Directory 的成员数目限制为 50,000。 如果需要同步超出此成员数限制的组，则必须载入 [Azure AD Connect 同步 V2 终结点 API](../articles/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2.md)。</li><li>并非所有方案都支持 Azure AD 中的嵌套组</li></ul><br/> 目前，以下是嵌套组支持的方案。<ul><li> 可以将一个组添加为另一个组的成员，并且可以实现组嵌套。</li><li> 组成员身份声明（当将应用程序配置为接收令牌中的组成员身份声明时，将包括登录用户所属的嵌套组）</li><li>条件访问（当条件访问策略具有组作用域时）</li><li>限制访问自助式密码重置</li><li>限制哪些用户可以进行 Azure AD 联接和设备注册</li></ul><br/>以下方案不支持嵌套组：<ul><li> 应用角色分配（支持向应用分配组，但嵌套在直接分配的组中的组将没有访问权限），可用于访问和预配</li><li>基于组的许可（将许可证自动分配给组的所有成员）</li><li>Microsoft 365 组。</li></ul> |
| 应用程序代理 | <ul><li>每个应用代理应用程序每秒最多 500 个事务</li><li>Azure AD 组织每秒最多 750 个事务</li></ul><br/>事务定义为单一 http 请求和对唯一资源的响应。 当受到限制时，客户端会收到 429 响应（请求过多）。 |
| 访问面板 |无论分配的许可证如何，每个用户可以在访问面板中看到的应用程序数量都没有限制。  |
| 报告 | 在报告中最多可查看或下载 1,000 行。 系统会截断其他任何数据。 |
| 管理单元 | Azure AD 资源可以是不超出 30 个管理单位的成员。 |
| Azure AD 角色和权限 | <ul><li>最多可在 Azure AD 组织中创建 30 个 [Azure AD 自定义角色](https://docs.microsoft.com/azure/active-directory//users-groups-roles/roles-custom-overview?context=azure/active-directory/users-groups-roles/context/ugr-context)。</li><li>无法将组添加为[组所有者](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)。</li><li>用户读取其他用户租户信息的能力只能通过以下方式限制：使用 Azure AD组织范围内的开关禁用所有非管理员用户对所有租户信息的访问（不推荐使用）。 有关详细信息，请参阅[限制成员用户的默认权限](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)。</li><li>在管理员角色成员身份添加和撤销生效之前，最多可能需要 15 分钟或注销/登录。</li></ul> |
