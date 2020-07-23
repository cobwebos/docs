---
title: 验证内置防火墙中的端口安全性
description: 了解如何在 Azure SQL 托管实例中验证内置防火墙保护。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 1d2fffabba3615394bdf96ed487177bf21f3ecec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708699"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>验证 Azure SQL 托管实例内置防火墙
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例[必需的入站安全规则](connectivity-architecture-overview.md#mandatory-inbound-security-rules)要求管理端口9000、9003、1438、1440和1452可从保护 SQL 托管实例的网络安全组（NSG）上的**任何源**打开。 虽然这些端口在 NSG 级别处于开放状态，但它们由内置防火墙在网络级别进行保护。

## <a name="verify-firewall"></a>验证防火墙

若要验证这些端口，请使用任何安全扫描程序工具来测试这些端口。 以下屏幕截图显示了如何使用这些工具之一。

![验证内置防火墙](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>后续步骤

有关 SQL 托管实例和连接的详细信息，请参阅[AZURE sql 托管实例连接体系结构](connectivity-architecture-overview.md)。
