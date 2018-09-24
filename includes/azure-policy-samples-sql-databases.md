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
ms.openlocfilehash: 70128735aef64d273c63236a13b0ae28edb6077d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003586"
---
### <a name="sql-databases"></a>SQL 数据库

|  |  |
|---------|---------|
| [允许的 SQL DB SKU](../articles/governance/policy/samples/allowed-sql-db-skus.md) | 要求 SQL 数据库使用已批准的 SKU。 指定一个允许的 SKU ID 的数组或允许的 SKU 名称的数组。 |
| [审核 DB 级别的威胁检测设置](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | 如果 SQL 数据库安全警报策略未设置成指定状态，则审核这些策略。 指定一个值，该值指示威胁检测是启用还是禁用状态。  |
| [审核 SQL 数据库加密](../articles/governance/policy/samples/sql-database-encryption-audit.md) | 如果 SQL 数据库未启用透明数据加密，则会进行审核。 |
| [审核 SQL DB 级别审核设置](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | 如果 SQL 数据库审核设置不匹配指定设置，则审核这些设置。 指定用于指示应启用或禁用审核设置的值。  |
| [审核透明数据加密状态](../articles/governance/policy/samples/audit-trans-data-enc-status.md) | 如果 SQL 数据库透明数据加密未启用，则审核该加密。  |