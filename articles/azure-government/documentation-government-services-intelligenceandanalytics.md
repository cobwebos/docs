---
title: "Azure 政府智能 + 分析 | Microsoft 文档"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: Azure-Government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 0302a44bb42200c07a313bf2862479c1722af8c4
ms.openlocfilehash: 2a8769f7a611fb22e39db166d3416728f57cbbf4


---
# <a name="azure-government-intelligence--analytics"></a>Azure 政府智能 + 分析
本文概述了 Azure 政府环境的智能和分析服务、变体和注意事项。

## <a name="azure-hdinsight"></a>Azure HDInsight
HDInsight 已在 Azure 政府中正式发布。

### <a name="variations"></a>变体
当前，以下 HDInsight 功能在 Azure 政府中不可用。

* HDInsight 在 Windows 上不可用。
* Azure Data Lake Store 目前在 Azure 政府中不可用。 Azure Blob 存储是目前唯一可用的存储选项。
* Azure Active Directory 域服务集成尚不可用。 若要创建安全的 Hadoop 群集而不使用 Active Directory 域服务，请选择以下设置方案之一：

1. 与 AZURE IAAS 上运行的 ACTIVE DIRECTORY 集成的 HDINSIGHT

  到目前为止，这是将 HDInsight 与 Active Directory 集成最简单的体系结构。 下面提供了体系结构示意图。 在此体系结构中，你将拥有一个在 Azure 中的一个（或多个）VM 上运行的 Active Directory 域控制器。 通常这些 VM 将位于一个虚拟网络中。 可以设置一个可在其中放置 HDInsight 群集的新虚拟网络。 要使 HDInsight 可以与 Active Directory 通信，需要使用 [VNET 到 VNET 对等互连] (https://docs.microsoft.com/zh-cn/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal) 将这些虚拟网络对等互连。 
  
  需要在 Active Directory 上设置的必备组件
     * 必须创建组织单位，该组织单位中将放置 HDInsight 群集 VM 和群集所用的服务主体。 
     * 必须设置 LDAPS，以便与 Active Directory 进行通信。 用于设置 LDAPS 的证书必须是实际证书（不是自签名证书）。
     * 必须针对 HDI 子网的 IP 地址范围在域上创建反向 DNS 区域。
     * 需要服务帐户或用户帐户，将使用这些帐户创建 HDInsight 群集。 此帐户必须具有以下权限
        * 在组织单位中创建服务主体对象和计算机对象的权限。
        * 创建反向 DNS 代理规则的权限。
        * 将计算机加入 Active Directory 域的权限。
        
2. 通过 VPN 设置与本地 ACTIVE DIRECTORY 集成的 HDINSIGHT
  
  此体系结构就像体系结构 #1。 唯一的区别是，在此示例中，Active Directory 在本地，HDInsight 到 Active Directory 的通信通过 [从 Azure 到本地网络的 VPN 连接] (https://docs.microsoft.com/zh-cn/azure/expressroute/expressroute-introduction)。 此设置的体系结构示意图如下所示。 
  
  需要在本地 Active Directory 上设置的必备组件
     * 必须创建组织单位，该组织单位中将放置 HDInsight 群集 VM 和群集所用的服务主体。
     * 必须设置 LDAPS，以便与 Active Directory 进行通信。 用于设置 LDAPS 的证书必须是实际证书（不是自签名证书）。
     * 必须针对 HDI 子网的 IP 地址范围在域上创建反向 DNS 区域。
     * 需要服务帐户或用户帐户，将使用这些帐户创建 HDInsight 群集。 此帐户必须具有以下权限
        * 在组织单位中创建服务主体对象和计算机对象的权限。
        * 创建反向 DNS 代理规则的权限。
        * 将计算机加入 Active Directory 域的权限。


Log Analytics 的 URL 在 Azure 政府版中是不同的：

| 服务类型 | Azure Public | Azure Government  |
| --- | --- | --- |
| HDInsight 群集 | \*.azurehdinsight.net | \*.azurehdinsight.us |

有关详细信息，请参阅 [Azure HDInsight 公共文档](../hdinsight/hdinsight-hadoop-introduction.md)。


## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 博客</a>。



<!--HONumber=Jan17_HO2-->


