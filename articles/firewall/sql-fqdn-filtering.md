---
title: 配置包含 SQL Fqdn 的 Azure 防火墙应用程序规则
description: 本文介绍如何在 Azure 防火墙应用程序规则中配置 SQL Fqdn。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 239998f29ac9a578174c5dba547bb24ba0755505
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318187"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>配置包含 SQL Fqdn 的 Azure 防火墙应用程序规则

> [!IMPORTANT]
> 包含 SQL Fqdn 的 Azure 防火墙应用程序规则当前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

你现在可以配置包含 SQL Fqdn 的 Azure 防火墙应用程序规则。 这允许你将对虚拟网络的访问限制为仅限指定的 SQL server 实例。

对于 SQL Fqdn, 你可以筛选流量:

- 从 Vnet 到 Azure SQL 数据库或 Azure SQL 数据仓库。 例如：仅允许访问*sql-server1.database.windows.net*。
- 从本地到 Azure SQL 托管实例或在你的 Vnet 中运行的 SQL IaaS。
- 从辐射到轮辐到在你的 Vnet 中运行的 Azure SQL 托管实例或 SQL IaaS。

在公共预览版中, 仅在[代理模式下](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)支持 SQL FQDN 筛选 (端口 1433)。 如果在默认重定向模式下使用 SQL, 则可以使用 SQL 服务标记作为[网络规则](overview.md#network-traffic-filtering-rules)的一部分来筛选访问。
如果将非默认端口用于 SQL IaaS 流量, 则可以在防火墙应用程序规则中配置这些端口。

使用 SQL Fqdn 的应用程序规则当前在所有区域中通过 Azure 门户、Azure CLI、REST 和模板可用。

## <a name="configure-using-azure-cli"></a>使用 Azure CLI 配置

1. [使用 Azure CLI 部署 Azure 防火墙](deploy-cli.md)。
2. 如果对 Azure SQL 数据库、SQL 数据仓库或 SQL 托管实例的流量进行筛选, 请确保将 SQL 连接模式设置为**Proxy**。 若要了解如何切换 SQL 连接模式, 请参阅[AZURE Sql 连接体系结构](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy)。 

   > [!NOTE]
   > 与*重定向*相比, SQL*代理*模式可能会导致更多的延迟。 如果要继续使用重定向模式, 这是在 Azure 中连接的客户端的默认设置, 可以使用防火墙[网络规则](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中的 SQL[服务标记](service-tags.md)筛选访问权限。

3. 使用 SQL FQDN 配置应用程序规则以允许访问 SQL server:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>使用 Azure 门户进行配置
1. [使用 Azure CLI 部署 Azure 防火墙](deploy-cli.md)。
2. 如果对 Azure SQL 数据库、SQL 数据仓库或 SQL 托管实例的流量进行筛选, 请确保将 SQL 连接模式设置为**Proxy**。 若要了解如何切换 SQL 连接模式, 请参阅[AZURE Sql 连接体系结构](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy)。 

   > [!NOTE]
   > 与*重定向*相比, SQL*代理*模式可能会导致更多的延迟。 如果要继续使用重定向模式, 这是在 Azure 中连接的客户端的默认设置, 可以使用防火墙[网络规则](tutorial-firewall-deploy-portal.md#configure-a-network-rule)中的 SQL[服务标记](service-tags.md)筛选访问权限。
3. 用适当的协议、端口和 SQL FQDN 添加应用程序规则, 然后选择 "**保存**"。
   ![包含 SQL FQDN 的应用程序规则](media/sql-fqdn-filtering/application-rule-sql.png)
4. 从 VNet 中的虚拟机访问 SQL, 以通过防火墙筛选流量。 
5. 验证[Azure 防火墙日志](log-analytics-samples.md)是否显示允许的流量。

## <a name="next-steps"></a>后续步骤

若要了解 SQL 代理和重定向模式, 请参阅[AZURE SQL 数据库连接体系结构](../sql-database/sql-database-connectivity-architecture.md)。