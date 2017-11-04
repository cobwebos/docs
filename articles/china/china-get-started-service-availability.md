---
title: "在中国可用的 Azure 服务 | Microsoft Docs"
description: "本文同时列出在全球 Azure 和 Azure 中国世纪互联可用的服务，以方便在两者之间进行比较。"
services: china
cloud: na
documentationcenter: na
author: v-wimarc
manager: edprice
ms.assetid: na
ms.service: china
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: v-wimarc
ms.openlocfilehash: 09d382e37e319ec7746c19bea46dfb380f077af3
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="azure-service-availability-in-china"></a>在中国可用的 Azure 服务

下列表格比较了在全球 Azure 和世纪互联运营的 Microsoft Azure（Azure 中国世纪互联）可用的服务。 在表格中，“GA”表示正式版，“预览版”表示以预览版提供的服务。

> [!NOTE]
> 本页中的表格已在 2017 年 8 月 23 日更新。 其中会不断添加新服务。 有关最新服务，请访问 [http://www.azure.cn/](http://www.azure.cn/)。

## <a name="compute-services"></a>计算服务
----------------

| 服务                   | 全球 Azure                                                                                                                                                                       | Azure 中国世纪互联                                                                                                         |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| 虚拟机           | A 系列基本版 <br>A 系列标准版 <br>Av2 标准版 <br>D 系列 <br>Dv2 系列 <br>F 系列 <br>E 系列 <br>G 系列 <br>L 系列 <br>M 系列 <br>N 系列 <br>H 系列 <br>磁盘加密 - Linux <br>磁盘加密 - Windows | A 系列基本版 <br>A 系列标准版 <br>Av2 标准版 <br>D 系列 <br>Dv2 系列 <br>F 系列 <br>磁盘加密 - Linux <br>磁盘加密 - Windows |
| 虚拟机规模集 | GA                                                                                                                                                                                 | GA                                                                                                                           |
| Azure 云服务       | GA                                                                                                                                                                                 | GA                                                                                                                           |
| Azure Batch                | GA                                                                                                                                                                                 | GA                                                                                                                           |
| Azure Service Fabric       | GA <br>适用于 Linux 的 Service Fabric（预览版）                                                                                                                                              | GA <br>适用于 Linux 的 Service Fabric（预览版）                                                                                        |
| Azure Functions            | GA                                                                                                                                                                                 | *不可用*                                                                                                              |
| Azure 容器服务    | GA                                                                                                                                                                                 | *不可用*                                                                                                              |
| Azure 容器注册表   | GA                                                                                                                                                                                 | *不可用*                                                                                                              |
| Azure 容器实例  | 预览                                                                                                                                                                            | *不可用*                                                                                                              |

## <a name="storage-services"></a>存储服务
----------------

| **服务**                                    | **全球 Azure**                                            | **Azure 中国世纪互联**                                    |
|-------------------------------------------------|-------------------------------------------------------------|-------------------------------------------------------------|
| Azure 存储（Blob、队列、文件、磁盘和表）  | 标准存储 <br>高级存储 <br>存储服务加密 | 标准存储 <br>高级存储 <br>存储服务加密 |
| Azure Blob 存储层                        | 热 <br>冷 <br>存档（预览版）                                  | 热 <br>冷                                                    |
| Azure StorSimple                                | StorSimple 5000 <br>StorSimple 7000 <br>StorSimple 8000             | StorSimple 5000 <br>StorSimple 7000                             |
| Azure 备份                                    | 文件备份 <br>IaaS VM 备份 <br>SQL Server 备份                | 文件备份 <br>IaaS VM 备份 <br>SQL Server 备份                |
| Azure Site Recovery                             | GA                                                          | GA                                                          |
| Azure 导入/导出                             | GA                                                          | GA                                                          |
| Azure 托管磁盘                             | GA                                                          | GA                                                          |

## <a name="networking-services"></a>网络服务
-------------------

| **服务**              | **全球 Azure**                                                                                     | **Azure 中国世纪互联**                                                                            |
|---------------------------|------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| 虚拟网络          | 静态/动态路由 VPN 网关 <br>标准 VPN 网关 <br>高性能 VPN 网关                 | 静态/动态路由 VPN 网关 <br>标准 VPN 网关 <br>高性能 VPN 网关                |
| Azure ExpressRoute        | 提供以下端口速度： <br>50 Mbps  <br>100 Mbps <br>200 Mbps <br>500 Mbps <br>1 Gbps <br>2 Gbps <br>5 Gbps <br>10 Gbps | 提供以下端口速度： <br>50 Mbps <br>100 Mbps <br>200 Mbps <br>500 Mbps <br>1 Gbps <br>2 Gbps <br>5 Gbps <br>10 Gbps |
| Azure DNS                 | GA                                                                                                   | *不可用*                                                                                     |
| Azure 流量管理器     | GA                                                                                                   | GA                                                                                                  |
| Azure 负载均衡器       | GA                                                                                                   | GA                                                                                                  |
| Azure 网络观察程序     | GA                                                                                                   | *不可用*                                                                                     |
| Azure 应用程序网关 | GA                                                                                                   | GA                                                                                                  |

## <a name="web-and-mobile-services"></a>Web 和移动服务
-----------------------

| **服务**            | **全球 Azure**                                                                                                                               | **Azure 中国世纪互联**                                                                       |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| Azure 应用服务       | 免费 <br>共享 <br>基本 <br>标准 <br>高级 <br>隔离  <br><br>以下服务可用： <br>Web 应用 <br>移动应用 <br>API 应用 <br>逻辑应用 <br>Linux 上的 Web 应用（预览版） | 免费 <br>共享 <br>基本 <br>标准 <br>高级  <br><br>仅以下服务可用： <br>Web 应用 <br>移动应用 <br>API 应用   |
| Azure Mobile Engagement | GA（即将停用）                                                                                                                                  | *不可用*                                                                                |
| Azure 搜索            | 免费 <br>基本 <br>标准 S1 <br>标准 S2 <br>标准 S3                                                                                                 | *不可用*                                                                                |

## <a name="databases"></a>数据库
---------

| **服务**                  | **全球 Azure**                                                                                                          | **Azure 中国世纪互联**                                                                                                  |
|-------------------------------|---------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| SQL 数据库                  | 单一数据库 <br>（基本、标准、高级、异地复制）  <br><br>弹性数据库 <br>（基本、标准、高级、异地复制） | 单一数据库 <br>（基本、标准、高级、异地复制）  <br><br>弹性数据库 <br>（基本、标准、高级、异地复制） |
| SQL 数据仓库            | GA                                                                                                                        | GA                                                                                                                        |
| SQL Server Stretch Database   | GA                                                                                                                        | GA                                                                                                                        |
| Azure Redis 缓存             | 基本 <br>标准 <br>高级                                                                                                    | 基本 <br>标准 <br>高级                                                                                                    |
| Azure Cosmos DB               | GA                                                                                                                        | GA                                                                                                                        |
| Azure Database for MySQL      | 预览                                                                                                                   | MS1-MS6、MP1、MP2                                                                                                       |
| Azure Database for PostgreSQL | 预览                                                                                                                   | *不可用*                                                                                                           |

## <a name="intelligence-and-analytics-services"></a>智能和分析服务
-----------------------------------

| **服务**              | **全球 Azure**                                                                                                                               | **Azure 中国世纪互联**                                                                                                                           |
|---------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Cortana Intelligence      | GA                                                                                                                                             | *不可用*                                                                                                                                                 |
| 认知服务        | 必应 API 和翻译工具 API 的 GA 版，其他 API 的预览版                                                                                       | 计算机视觉 API、人脸 API 和情感 API 的预览版                                                                                          |
| Azure HDInsight           | Hadoop、HBase、Storm、Spark、R Server、Kafka（预览版）、交互式 Hive（预览版）  <br><br>常规用途节点 <br>计算密集型节点 <br>优化节点 | Hadoop、HBase、Storm、Spark、R Server、Kafka（预览版）、交互式 Hive（预览版）  <br><br>常规用途节点 <br>计算密集型节点 <br>优化节点     |
| Azure 机器学习    | 免费 <br>标准                                                                                                                                  | *不可用*                                                                                                                                    |
| Azure 流分析    | GA                                                                                                                                             | GA                                                                                                                                                 |
| Azure Bot 服务         | 预览                                                                                                                                        | *不可用*                                                                                                                                    |
| Azure 数据工厂        | 低频率 <br>高频率                                                                                                                   | *不可用*                                                                                                                                    |
| Azure Data Lake Store     | GA                                                                                                                                             | *不可用*                                                                                                                                    |
| Azure Data Lake Analytics | GA                                                                                                                                             | *不可用*                                                                                                                                    |
| Microsoft Power BI SaaS   | GA                                                                                                                                             | GA                                                                                                                                                 |
| Power BI PaaS (Embedded)  | GA                                                                                                                                             | GA                                                                                                                                                 |

## <a name="internet-of-things-iot-services"></a>物联网 (IoT) 服务
---------------------------------

| **服务**            | **全球 Azure**         | **Azure 中国世纪互联** |
|-------------------------|--------------------------|--------------------------|
| Azure 事件中心        | 基本 <br>标准 <br>专用 | 基本 <br>标准           |
| Azure IoT 中心           | GA                       | GA                       |
| Azure IoT 套件         | GA                       | GA                       |
| Azure 流分析  | GA                       | GA                       |
| Azure 通知中心 | 免费 <br>基本 <br>标准      | 免费 <br>基本 <br>标准      |

## <a name="media-services-and-azure-content-delivery-network-cdn"></a>媒体服务和 Azure 内容交付网络 (CDN)
-------------------------------------------------------

| **服务**    | **全球 Azure**                                                                                  | **Azure 中国世纪互联**        |
|-----------------|---------------------------------------------------------------------------------------------------|---------------------------------|
| 媒体服务  | 编码 <br>索引 <br>流式处理 <br>DRM <br>（支持多重 DRM：PlayReady、Widevine <br>和 FairPlay 流式处理） | 编码 <br>索引 <br>流式处理 <br>DRM |
| CDN             | 由 EdgeCast 提供                                                                              | 由本地 CDN 提供商提供  |

## <a name="enterprise-integration"></a>企业集成
----------------------

| **服务**           | **全球 Azure**           | **Azure 中国世纪互联** |
|------------------------|----------------------------|--------------------------|
| Azure BizTalk 服务 | GA（即将停用）              | *不可用*          |
| Azure 服务总线      | 基本 <br>标准 <br>高级     | 基本 <br>标准           |
| Azure API 管理   | 开发人员 <br>标准 <br>高级 | *不可用*          |
| Azure 逻辑应用       | GA                         | *不可用*          |
| Azure 数据目录     | 免费 <br>标准              | *不可用*          |
| Azure 事件网格       | 预览                    | *不可用*          |

## <a name="security-and-identity-services"></a>安全和标识服务
------------------------------

| **服务**                      | **全球 Azure**   | **Azure 中国世纪互联**                          |
|-----------------------------------|--------------------|---------------------------------------------------|
| Azure Active Directory            | 免费 <br>基本 <br>高级 | 免费 <br>（自助密码更改可用）  |
| Azure Active Directory B2C        | GA                 | *不可用*                                   |
| Azure AD 域服务          | GA                 | *不可用*                                   |
| Azure 多重身份验证 | GA                 | GA                                                |
| Azure 密钥保管库                   | 标准 <br>高级   | 标准 <br>高级  <br>无 HSM 产品                 |
| Azure 安全中心             | 免费 <br>标准      | *不可用*                                   |

## <a name="developer-tools"></a>开发人员工具
---------------

| **服务**                | **全球 Azure**            | **Azure 中国世纪互联** |
|-----------------------------|-----------------------------|--------------------------|
| Visual Studio Team Services | 基本 <br>专业版 <br>高级 | *不可用*          |
| Application Insights        | 免费 <br>基本 <br>Enterprise       | *不可用*          |
| Azure 开发测试实验室         | 免费                        | *不可用*          |
| HockeyApp                   | GA                          | *不可用*          |

## <a name="monitoring-and-management"></a>监视和管理
-------------------------

| **服务**           | **全球 Azure**                             | **Azure 中国世纪互联** |
|------------------------|----------------------------------------------|--------------------------|
| Azure 门户           | GA                                           | GA                       |
| Azure 资源管理器 | GA                                           | GA                       |
| 应用商店            | GA                                           | 预览                  |
| 计划程序              | 免费 <br>标准 <br>高级                        | 免费 <br>标准 <br>高级    |
| 自动化             | 免费 <br>基本 <br>Desired State Configuration <br>(DSC) | 免费 <br>基本               |
| Log Analytics          | 免费 <br>独立                              | *不可用*          |
| OMS                    | E1 <br>E2                                        | *不可用*          |
| Azure 监视器          | GA                                           | *不可用*          |
| Azure 顾问          | 免费                                         | *不可用*          |


## <a name="next-steps"></a>后续步骤
- [检查新服务](http://www.azure.cn/)


