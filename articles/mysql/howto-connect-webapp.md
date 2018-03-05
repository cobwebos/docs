---
title: "将现有的 Azure 应用服务连接到 Azure Database for MySQL"
description: "有关如何正确地将现有的 Azure 应用服务连接到 Azure Database for MySQL 的说明"
services: mysql
author: ajlam
ms.author: andrela
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: d8b130876e5fa0f2b2322dff82013a409ff7d30e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>将现有的 Azure 应用服务连接到 Azure Database for MySQL 服务器
本主题将说明如何将现有的 Azure App Service 连接到 Azure Database for MySQL 服务器。

## <a name="before-you-begin"></a>开始之前
登录到 [Azure 门户](https://portal.azure.com)。 创建 Azure Database for MySQL 服务器。 有关详细信息，请参阅[如何在门户中创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)或[如何使用 CLI 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)。

当前有两种解决方案，都允许从 Azure 应用服务访问 Azure Database for MySQL。 这两种解决方案都涉及设置服务器级别的防火墙规则。

## <a name="solution-1---create-a-firewall-rule-to-allow-all-ips"></a>解决方案 1 - 创建允许所有 IP 的防火墙规则
Azure Database for MySQL 通过使用防火墙保护数据来确保访问安全。 从 Azure App Service 连接到 Azure Database for MySQL 服务器时，请记住应用服务的出站 IP 实际上为动态 IP。 

为了确保 Azure 应用服务的可用性，建议使用此解决方案允许所有 IP。

> [!NOTE]
> Microsoft 正在研究长期的解决方案，避免允许 Azure 服务的所有 IP 连接到 Azure Database for MySQL。

1. 在 MySQL 服务器边栏选项卡上的“设置”标题下，单击“连接安全性”，以打开 Azure Database for MySQL 的“连接安全性”边栏选项卡。

   ![Azure 门户 - 单击连接安全性](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. 输入“规则名称”、“起始 IP”和“结束 IP”，然后单击“保存”。
   - 规则名称：Allow-All-IPs
   - 起始 IP：0.0.0.0
   - 结束 IP：255.255.255.255

   ![Azure 门户 - 添加所有 IP](./media/howto-connect-webapp/1_2-add-all-ips.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>解决方案 2 - 创建显式允许出站 IP 的防火墙规则
可显式添加 Azure 应用服务的所有出站 IP。

1. 在应用服务的“属性”边栏选项卡中，查看“出站 IP 地址” 。

   ![Azure 门户 - 查看出站 IP](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. 在 MySQL 的“连接安全性”边栏选项卡中，逐个添加出站 IP。

   ![Azure 门户 - 添加显式 IP](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. 请记住保存防火墙规则。

尽管 Azure App Service 会尝试在一段时间内将 IP 地址保持不变，但也有 IP 地址可能发生变动的情况。 例如，当应用回收、出现缩放操作或在 Azure 区域数据中心添加新计算机以增加容量时，IP 地址可能发生变动。 IP 地址变动时，应用若无法再连接到 MySQL 服务器，则可能会停机。 选择上述任何一种解决方案时，请考虑此可能性。

## <a name="ssl-configuration"></a>SSL 配置
Azure Database for MySQL 已默认启用 SSL。 如果应用程序不使用 SSL 连接到数据库，则需禁用 MySQL 服务器上的 SSL。 有关如何配置 SSL 的详细信息，请参阅[通过 Azure Database for MySQL 使用 SSL](howto-configure-ssl.md)。

## <a name="next-steps"></a>后续步骤
有关连接字符串的详细信息，请参阅[连接字符串](howto-connection-string.md)。
