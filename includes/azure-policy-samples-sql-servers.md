---
title: include 文件
description: include 文件
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664433"
---
### <a name="sql-servers"></a>SQL Server

|  |  |
|---------|---------|
| [无 Azure Active Directory 管理员时审核](../articles/azure-policy/scripts/audit-no-aad-admin.md) | SQL 服务器未分配有任何 Azure Active Directory 管理员时进行审核。 |
| [审核服务器级别的威胁检测设置](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | 如果 SQL 数据库安全警报策略未设置成指定状态，则审核这些策略。 指定一个值，该值指示威胁检测是启用还是禁用状态。  |
| [审核 SQL Server 审核设置](../articles/azure-policy/scripts/sql-server-audit.md) | 基于是否启用了审核设置审核 SQL Server。 |
| [审核 SQL Server 级别审核设置](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | 如果 SQL 服务器审核设置不匹配指定的设置，则审核这些设置。 指定用于指示应启用或禁用审核设置的值。 |
| [需要 SQL Server 版本 12.0](../articles/azure-policy/scripts/req-sql-12.md) | 要求 SQL 服务器使用版本 12.0。  |