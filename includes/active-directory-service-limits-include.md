---
title: include 文件
description: include 文件
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 07/30/2018
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: a2055c3f00306fbfdad3028a16bb49d919e1e251
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39361367"
---
下面是 Azure Active Directory (Azure AD) 服务的使用限制和其他服务限制。

| 类别 | 限制 |
| --- | --- |
| 目录 | 单个用户最多可以是 500 个 Azure AD 目录的成员或来宾。<br/>单个用户最多可以创建 20 个目录。 |
| 域 | 可以添加不超过 900 个的托管域名。 若要将所有域设置为与本地 Active Directory 联合，则可以在每个目录中添加不超过 450 个的域名。 |
| 对象 |<ul><li>Azure Active Directory 免费版用户最多可以在单个目录中创建 500,000 个对象。</li><li>非管理员用户最多可以创建 250 个对象。</li></ul> |
| 架构扩展 |<ul><li>String 类型扩展最多只能有 256 个字符。 </li><li>Binary 类型扩展限制在 256 字节以内。</li><li>100 扩展值（在 ALL 类型和 ALL 应用程序中）可以编写到任何单一对象中。</li><li>仅“用户”、“组”、“TenantDetail”、“设备”、“应用程序”和“ServicePrincipal” 实体可以用“String”类型或“Binary”类型单一值属性进行扩展。</li><li>架构扩展仅在 Graph API 1.21 预览版中可用。 必须授予应用程序编写访问注册扩展的权限。</li></ul> |
| 应用程序 |最多有 100 位用户可以是单一应用程序的所有者。 |
| 组 |<ul><li>最多有 100 位用户可以是单一组的所有者。</li><li>Azure Active Directory 中的单个组可以具有任意数量的对象。</li><li>使用 Azure AD Connect 时，一个小组中从本地 Active Directory 同步到 Azure Active Directory 的成员数目仅限 50000。</li></ul> |
| 访问面板 |<ul><li>对应用程序的数量没有限制，应用程序可在每位获得 Azure AD Premium 或企业移动套件许可的最终用户的“访问面板”中查看。</li><li>每位获得免费版或 Azure AD 基本版 Azure Active Directory 许可的最终用户最多可在“访问面板”中查看 10 个应用磁贴（例如：Box、Salesforce 或 Dropbox）。 此限制不适用于管理员帐户。</li></ul> |
| 报告 | 在报告中最多可查看或下载 1,000 行。 系统会截断其他任何数据。 |
| 管理单元 | 对象可以是不超过 30 个管理单位的成员。 |
