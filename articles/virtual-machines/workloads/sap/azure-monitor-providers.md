---
title: SAP 解决方案提供商的 Azure Monitor |Microsoft Docs
description: 本文提供有关 Azure monitor for SAP 解决方案的常见问题的解答
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: a7e44046de3eccab83e8315e6adea150a146e660
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964186"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>适用于 SAP 解决方案提供商的 Azure monitor （预览版）

## <a name="overview"></a>概述  

在 SAP 解决方案 Azure Monitor 的上下文中，*提供程序类型*引用特定的*提供程序*。 例如*SAP HANA*，该配置针对 SAP 布局中的特定组件（如 SAP HANA 数据库）。 提供程序包含相应组件的连接信息，有助于从该组件收集遥测数据。 可以为 SAP 解决方案资源（也称为 SAP 监视器资源）的一个 Azure Monitor 配置同一提供程序类型或多个提供程序类型的多个提供程序的多个提供程序。
   
客户可以选择配置不同的提供程序类型，以在其 SAP 环境中从相应组件启用数据收集。 例如，客户可以为 SAP HANA 提供程序类型配置一个提供程序，为高可用性群集提供程序类型配置一个提供程序，等等。  

客户还可以选择配置特定提供程序类型的多个提供程序，以重复使用相同的 SAP 监视器资源和关联的托管组。 了解有关托管资源组的详细信息。 对于公共预览版，支持以下提供程序类型：   
- SAP HANA
- 高可用性群集
- Microsoft SQL Server

![SAP 解决方案提供商的 Azure Monitor](./media/azure-monitor-sap/azure-monitor-providers.png)

建议在部署 SAP 监视器资源时，从可用提供程序类型配置至少一个提供程序。 通过配置提供程序，客户可以从为其配置提供程序的相应组件启动数据收集。   

如果客户在部署 SAP 监视器资源时未配置任何提供程序，尽管 SAP monitor 资源会成功部署，但不会收集遥测数据。 客户可以选择在部署后通过 SAP monitor 资源在 Azure 门户中添加提供程序。 客户可随时在 SAP monitor 资源中添加或删除提供程序。

> [!Tip]
> 如果你想要 Microsoft 实施特定的提供商，请通过本文档结尾处的链接离开反馈，或联系你的帐户团队。  

## <a name="provider-type-sap-hana"></a>提供程序类型 SAP HANA

客户可以配置一个或多个提供程序类型的提供程序， *SAP HANA*以启用 SAP HANA 数据库中的数据收集。 SAP HANA 提供程序通过 SQL 端口连接到 SAP HANA 数据库，从数据库中提取遥测数据，并将其推送到客户订阅中的 "Log Analytics" 工作区。 SAP HANA 提供程序从 SAP HANA 数据库每隔1分钟收集一次数据。  

在公共预览版中，客户可以使用 SAP HANA 提供程序查看以下数据：底层基础结构利用率、SAP HANA 主机状态、SAP HANA 系统复制和 SAP HANA 备份遥测数据。 若要配置 SAP HANA 提供程序、主机 IP 地址、HANA SQL 端口号和 SYSTEMDB 用户名和密码。 建议客户针对 SYSTEMDB 配置 SAP HANA 提供程序，但可以针对其他数据库租户配置其他提供程序。

![SAP 解决方案提供商的 Azure Monitor-SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>提供程序类型高可用性群集
客户可以配置一个或多个提供程序类型的提供程序类型的*高可用性群集*，以便在 SAP 环境内从 Pacemaker 群集进行数据收集。 高可用性群集提供程序使用[ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter)终结点连接到 Pacemaker，从数据库中提取遥测数据，并将其推送到客户订阅中 Log Analytics 工作区。 高可用性群集提供程序从 Pacemaker 每60秒收集数据。  

在公共预览版中，客户可以通过高可用性群集提供商查看以下数据：   
 - 群集状态以节点和资源状态的汇总形式表示 
 - [其余](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![SAP 解决方案提供商的 Azure Monitor-高可用性群集](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

若要配置高可用性群集提供程序，需要执行两个主要步骤： 
1. 在 Pacemaker 群集中的*每个*节点上安装[ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 
    - 客户可以使用 Azure 自动化脚本来部署高可用性群集。 脚本将在每个群集节点上安装[ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) 。  
    - 或客户可以执行手动安装，请按照[此页](https://github.com/ClusterLabs/ha_cluster_exporter)上的步骤进行操作 
2. 在 Pacemaker 群集中的*每个*节点上配置高可用性群集提供程序  
  若要配置高可用性群集提供程序，需要 Prometheus URL、群集名称、主机名和系统 ID。   
  建议客户为每个群集节点配置一个提供程序。   

## <a name="provider-type-microsoft-sql-server"></a>提供程序类型 Microsoft SQL server

客户可以配置一个或多个提供程序类型的提供程序， *Microsoft SQL Server*从[虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)启用数据收集。 SQL Server 提供程序通过 SQL 端口连接到 Microsoft SQL Server，从数据库中提取遥测数据，并将其推送到客户订阅中的 "Log Analytics" 工作区。 必须为 SQL 身份验证配置 SQL Server，并使用 SAP DB 作为提供程序的默认数据库的 SQL Server 登录名进行配置。 SQL Server 提供程序从 SQL Server 每隔60秒收集一次数据。  

在公共预览版中，客户可以使用 SQL Server 提供程序查看以下数据：底层基础结构利用率、顶级 SQL 语句、顶级最大表、SQL Server 错误日志中记录的问题、阻止进程和其他内容。  

若要配置 Microsoft SQL Server 提供程序，则需要 SAP 系统 ID、主机 IP 地址、SQL Server 端口号以及 SQL Server 登录名和密码。

![SAP 解决方案提供商的 Azure Monitor-SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>后续步骤

- 创建适用于 SAP 解决方案资源的第一个 Azure Monitor。
- 对于 SAP 解决方案 Azure Monitor 是否有疑问？ 检查[FAQ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/azure-monitor-faq)部分
