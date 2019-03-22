---
title: include 文件
description: include 文件
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554797"
---
下面是 Azure Active Directory (Azure AD) 服务的使用限制和其他服务限制。

| 类别 | 限制 |
| --- | --- |
| 目录 | 单个用户最多可以是 500 个 Azure AD 目录的成员或来宾。<br/>单个用户最多可以创建 20 个目录。 |
| 域 | 可以添加不超过 900 个的托管域名。 如果设置了所有域以便与本地 Active Directory 联合身份验证时，可以在每个目录中添加不超过 450 个域名。 |
| 对象 |<ul><li>Azure Active Directory 免费版用户最多可以在单个目录中创建 500,000 个对象。</li><li>非管理员用户最多可以创建 250 个对象。 活动对象和已删除的对象可用于还原计入此配额。 仅已删除的已删除的对象少于 30 天前可供还原。 已删除的对象将不再可用于还原计入此配额在值为 30 天的四分之一。 也许[分配管理员角色](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md)到非管理员用户，他们可能会重复超过此配额在其日常任务的过程中。</li></ul> |
| 架构扩展 |<ul><li>字符串类型扩展最多可以输入 256 个字符。 </li><li>Binary 类型扩展被限制为 256 个字节。</li><li>只有 100 扩展值，跨*所有*类型并*所有*应用程序，可以编写到任何单一对象。</li><li>唯一的用户、 组、 TenantDetail、 设备、 应用程序中，和 ServicePrincipal 实体可以用字符串类型或二进制类型单值属性进行扩展。</li><li>仅在 Graph API 版本 1.21 预览版中提供了架构扩展。 必须授予应用程序编写访问注册扩展的权限。</li></ul> |
| 应用程序 |最多有 100 位用户可以是单一应用程序的所有者。 |
| 组 |<ul><li>最多有 100 位用户可以是单一组的所有者。</li><li>任意数量的对象都可以是单个组的成员。</li><li>一个用户可以是任意数量的组的成员。</li><li>可以通过同步本地 Active Directory 到 Azure Active Directory 使用 Azure AD Connect 组中的成员数被限制为 50,000 个成员。</li></ul> |
| 访问面板 |<ul><li>可以看到每个用户在访问面板中的应用程序数量没有限制。 这适用于 Azure AD Premium 或企业移动性套件分配许可证的用户。</li><li>可以为每个用户在访问面板中看到最多 10 个应用磁贴。 此限制适用于免费分配许可证的用户或 Azure Active Directory 的 Azure AD 基本版。 应用磁贴的示例包括框、 Salesforce 或 Dropbox。 此限制不适用于管理员帐户。</li></ul> |
| 报告 | 在报告中最多可查看或下载 1,000 行。 系统会截断其他任何数据。 |
| 管理单元 | 对象可以是不超过 30 个管理单位的成员。 |
| 管理员角色和权限 | <li>不能将组添加为所有者。<li>组不能分配给一个角色。<li>默认的用户权限不能更改租户切换，这是在 Azure AD 中的用户的设置除外。 |
