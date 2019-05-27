---
title: include 文件
description: include 文件
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: c8453a2ec00a2fca107f85a23a8af1e6313a70b6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155410"
---
### <a name="sql-servers"></a>SQL Server

|  |  |
|---------|---------|
| [无 Azure Active Directory 管理员时审核](../articles/governance/policy/samples/audit-no-aad-admin.md) | SQL 服务器未分配有任何 Azure Active Directory 管理员时进行审核。 |
| [审核服务器级别的威胁检测设置](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | 如果 SQL 数据库安全警报策略未设置成指定状态，则审核这些策略。 指定一个值，该值指示威胁检测是启用还是禁用状态。  |
| [审核 SQL Server 审核设置](../articles/governance/policy/samples/sql-server-audit.md) | 基于是否启用了审核设置审核 SQL Server。 |
| [审核 SQL Server 级别审核设置](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | 如果 SQL 服务器审核设置不匹配指定的设置，则审核这些设置。 指定用于指示应启用或禁用审核设置的值。 |
| [需要 SQL Server 版本 12.0](../articles/governance/policy/samples/require-sql-12.md) | 要求 SQL 服务器使用版本 12.0。  |