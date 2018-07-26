---
title: 服务器级别防火墙规则
description: 服务器级别防火墙规则
keywords: sql 连接, 连接字符串
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 07bbba28b7e1b048ef3a4e1afa4db255967dc093
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117267"
---
1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 在左侧的列表中，选择“所有服务”。

3. 滚动并选择“SQL Server”。

    ![在门户中找到 Azure SQL 数据库服务器][b21-FindServerInPortal]
5. 在筛选器文本框中，开始键入服务器的名称。 此时会显示行。

6. 选择服务器所对应的行。 此时会显示服务器的边栏选项卡。

7. 在服务器边栏选项卡上选择“设置”。

8. 选择“防火墙”。

    ![选择“设置”>“防火墙”][b31-SettingsFirewallNavig]
9. 选择“添加客户端 IP”。 在第一个文本框中键入新规则的名称。

10. 键入要启用的范围的下限和上限 IP 地址值。

    * 为方便起见，可以让下限值以 **.0** 结尾，让上限值以 **.255** 结尾。

11. 选择“保存”。

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
