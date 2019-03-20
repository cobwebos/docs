---
title: 发现 Azure SQL 数据库托管实例内置防火墙 | Microsoft Docs
description: 了解如何验证 Azure SQL 数据库托管实例中的内置防火墙保护。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: f059ac4149a385a12b440db0ad6394a343a1f810
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761916"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>验证托管实例内置防火墙

托管实例[强制入站安全规则](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules)要求管理端口 9000、9003、1438、1440、1452 从保护该托管示例的网络安全组 (NSG) 上的**任何源**中开放。 虽然这些端口在 NSG 级别处于开放状态，但它们由内置防火墙在网络级别进行保护。

## <a name="verify-firewall"></a>验证防火墙

若要验证这些端口，请使用任何安全扫描程序工具来测试这些端口。 以下屏幕截图显示了如何使用这些工具之一。

![验证内置防火墙](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>后续步骤

有关托管实例和连接的详细信息，请参阅 [Azure SQL 数据库托管实例连接体系结构](sql-database-managed-instance-connectivity-architecture.md)。