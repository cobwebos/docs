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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472208"
---
下面是 Azure Active Directory (Azure AD) 服务的使用限制和其他服务限制。

| 类别 | 限制 |
| --- | --- |
| 目录 | 单个用户最多可以是 500 个 Azure AD 目录的成员或来宾。<br/>单个用户最多可以创建 20 个目录。 |
| 域 | 可以添加不超过 900 个的托管域名。 如果将所有域设置为与本地 Active Directory 联合，则可在每个目录中添加不超过 450 个域名。 |
| 对象 |<ul><li>Azure Active Directory 免费版用户最多可以在单个目录中创建 500,000 个对象。</li><li>非管理员用户最多可以创建 250 个对象。 活动的对象以及已删除但可用于还原的对象都会计入此配额中。 只有那些已删除但删除的时间不到 30 天的对象可以用来还原。 在 30 天的时间内，已删除且不再能够用来还原的对象在计入此配额时，其值按四分之一计算。 也许可以向那些可能会在执行日常任务的过程中反复超过此配额的非管理员用户[分配管理员角色](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md)。</li></ul> |
| 架构扩展 |<ul><li>字符串类型扩展最多只能有 256 个字符。 </li><li>二进制类型扩展限制在 256 字节以内。</li><li>只能将 100 个扩展值（包括所有类型和所有应用程序）写入到任何单一对象中。</li><li>仅“用户”、“组”、“TenantDetail”、“设备”、“应用程序”和“ServicePrincipal”实体可以用字符串类型或二进制类型单一值属性进行扩展。</li><li>架构扩展仅在 Graph API 1.21 预览版中可用。 必须授予应用程序编写访问注册扩展的权限。</li></ul> |
| 应用程序 |最多有 100 位用户可以是单一应用程序的所有者。 |
| 组 |<ul><li>最多有 100 位用户可以是单一组的所有者。</li><li>任意数量的对象都可以是单个组的成员。</li><li>一个用户可以是任意数量的组的成员。</li><li>使用 Azure AD Connect 时，一个小组中从本地 Active Directory 同步到 Azure Active Directory 的成员数目仅限 50,000。</li></ul> |
| 访问面板 |<ul><li>对于可以在访问面板中按用户查看的应用程序，其数量没有限制。 这适用于 Azure AD Premium 或企业移动性套件的用户分配许可证。</li><li>每位用户最多可以在访问面板中看到 10 个应用磁贴。 此限制适用于获得免费版或 Azure AD 基本版 Azure Active Directory 许可证的用户。 Box、Salesforce、Dropbox 都是应用磁贴的示例。 此限制不适用于管理员帐户。</li></ul> |
| 报告 | 在报告中最多可查看或下载 1,000 行。 系统会截断其他任何数据。 |
| 管理单元 | 对象可以是不超过 30 个管理单位的成员。 |
| 管理员角色和权限 | <li>无法将组添加为所有者。<li>无法将组分配给角色。<li>无法更改租户开关（Azure AD 中的用户设置）之外的默认用户权限。 |
