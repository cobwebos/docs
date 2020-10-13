---
title: 常见问题解答-SAP 解决方案的 Azure Monitor |Microsoft Docs
description: 本文介绍常见问题的答案 (常见问题解答) 有关 Azure monitor for SAP 解决方案的常见问题解答。
author: rdeltcheva
ms.service: virtual-machines
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: f584601c2dcbea989f1b68d9fbac5a7f53a59d86
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994214"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Azure monitor for SAP 解决方案常见问题 (预览版) 
## <a name="frequently-asked-questions"></a>常见问题

本文提供常见问题的解答 (常见问题解答) 有关 Azure monitor for SAP 解决方案的常见问题解答。  

 - **我是否必须为 SAP 解决方案的 Azure Monitor 付费？**  
对于 SAP 解决方案的 Azure Monitor，没有许可费。  
然而，客户需要负责管理资源组组件的成本。  

 - **此服务可用于公共预览版的区域是什么？**  
对于公共预览版，此服务将在美国东部2、美国西部2、美国东部和西欧提供。  

 - **我是否需要提供权限以允许在我的订阅中部署托管资源组？**  
不需要，显式权限不是必需的。  

 - **收集器 VM 位于何处？**  
部署 SAP 解决方案资源的 Azure Monitor 时，我们建议客户选择与用于监视资源的 VNET 相同的 VNET 作为 SAP HANA 服务器。 因此，建议将收集器 VM 与 SAP HANA server 位于同一 VNET 中。 如果客户使用的是非 HANA 数据库，收集器 VM 将与非 HANA 数据库驻留在同一 VNET 中。  

 - **支持哪些版本的 HANA？**  
HANA 1.0 SPS 12 (120 或更高版本) 和 HANA 2.0 SPS03 或更高版本  

 - **支持哪些 HANA 部署配置？**  
支持以下配置：
   - 单节点 (向上扩展) 和多节点 (横向扩展)   
   - 单个数据库容器 (HANA 1.0 SPS 12) 和多个数据库容器 (HANA 1.0 SPS 12 或 HANA 2.0)   
   - 自动主机故障转移 (n + 1) 和 HSR  

 - **支持哪些 SQL Server 版本？**  
SQL Server 2012 SP4 或更高版本。  

 - **支持哪些 SQL Server 配置？**  
支持以下配置：
   - 虚拟机中的默认或命名独立实例  
   - 使用群集资源的虚拟名称或 AlwaysOn 侦听器名称时，使用 AlwaysOn 配置中的群集实例或实例。 当前没有收集群集或 AlwaysOn 特定度量值    
   - 当前不支持 Azure SQL 数据库 (PAAS)   

 - **如果意外删除了托管资源组，会发生什么情况？**  
默认情况下，托管资源组处于锁定状态。 因此，客户意外删除该托管资源组的机会 minuscule。  
如果客户删除托管资源组，则 SAP 解决方案 Azure Monitor 将停止工作。 客户必须为 SAP 解决方案资源部署新的 Azure Monitor，然后重新开始。  

 - **我的 Azure 订阅中需要哪些角色才能部署 SAP 解决方案资源 Azure Monitor？**  
参与者角色。  

 - **此产品的 SLA 是什么？**  
从服务级别协议中排除了预览。 请参阅 SAP 解决方案 marketplace Azure Monitor 的完整许可条款。  

 - **能否通过此解决方案监视整个环境？**  
当前可以在公共预览版中监视 HANA 数据库、底层基础结构、高可用性群集和 Microsoft SQL server。  

 - **此服务是否替换 SAP 解决方案管理器？**  
否。 客户仍可使用 SAP 解决方案管理器进行业务流程监视。  

 - **此服务与 SAP HANA 考核中心/工作室等传统解决方案的价值是什么？**  
SAP 解决方案的 Azure Monitor 不特定于 HANA 数据库。 SAP 解决方案 Azure Monitor 也支持 AnyDB。  

## <a name="next-steps"></a>后续步骤

- 创建适用于 SAP 解决方案资源的第一个 Azure Monitor。
