---
title: 部署体系结构 - Azure 专用 HSM | Microsoft Docs
description: 将 Azure 专用 HSM 用作应用程序体系结构的一部分时的基本设计注意事项
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: f078df7677e771d131f15056ac4a54a58a3134bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60912251"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Azure 专用 HSM 部署体系结构

Azure 专用 HSM 提供 Azure 中的加密密钥存储。 它符合严格的安全要求。 如果客户具备以下条件，将受益于 Azure 专用 HSM：

* 必须符合 FIPS 140-2 级别 3 认证
* 要求其具有对 HSM 的独占访问权限
* 应具有对其设备的完全控制权

HSM 分布于 Microsoft 数据中心，并可以作为高度可用解决方案的基础轻松地预配为设备对。 它们还可以跨区域部署，用于灾难恢复解决方案。 专用 HSM 可用的区域目前为：

* 美国东部
* 美国东部 2
* 美国西部
* 美国中南部
* 东南亚
* 东亚
* 北欧
* 西欧
* 英国南部
* 英国西部
* 加拿大中部
* 加拿大东部
* 澳大利亚东部
* 澳大利亚东南部

以上每个区域在两个独立数据中心或至少两个独立可用性区域中部署 HSM 机架。 东南亚有三个可用性区域，美国东部 2 有两个。 在欧洲、亚洲和美国总共有八个区域提供专用 HSM 服务。 有关 Azure 区域的详细信息，请参阅官方 [Azure 区域信息](https://azure.microsoft.com/global-infrastructure/regions/)。
任意基于专用 HSM 的解决方案共享的一些设计因素包括位置/延迟、高可用性，以及对其他分布式应用程序的支持。

## <a name="device-location"></a>设备位置

HSM 设备最佳位置是最接近执行加密操作的应用程序的位置。 区域内延迟预计为个位毫秒数。 跨区域延迟可能是这的 5 到 10 倍。

## <a name="high-availability"></a>高可用性

若要实现高可用性，客户必须在区域中使用通过 Gemalto 软件配置为高可用性对的两个 HSM 设备。 这种部署可确保在单个设备遇到阻碍其处理密钥操作的问题时密钥的可用性。 执行中断/修复维护（如电源更换）时，它还可以极大地降低风险。 对设计来说，说明各种区域级故障的原因非常重要。 遭遇自然灾害（如飓风、洪水或地震）时，可能会发生区域级故障。 应在另一个区域预配 HSM 设备以减轻此类事件的影响。 另一个区域部署的设备可通过 Gemalto 软件配置进行配对。 这意味着，跨两个区域的高可用性和灾难复原解决方案的最小部署为四个 HSM 设备。 本地冗余和跨区域冗余可作为基线以添加其他更多的 HSM 设备部署来支持延迟和容量，或者满足其他特定于应用程序的要求。

## <a name="distributed-application-support"></a>分布式应用程序支持

通常部署专用 HSM 设备来支持需要执行密钥存储和密钥检索操作的应用程序。 专用 HSM 设备具有 10 个分区用于独立的应用程序支持。 设备位置应取决于所有需要使用该服务的应用程序的整体情况。

## <a name="next-steps"></a>后续步骤

确定部署体系结构后，Gemalto 将提供实现该体系结构的大多数配置活动。 这包括设备配置以及应用程序集成方案。 有关详细信息，请使用 [Gemalto 客户支持](https://supportportal.gemalto.com/csm/)门户并下载管理和配置指南。 Microsoft 合作伙伴网站提供各种集成指南。
建议在进行设备预配或应用程序设计和部署之前，先深入了解此服务的所有重要概念（例如高可用性和安全性）。
更多概念级别的主题：

* [高可用性](high-availability.md)
* [物理安全性](physical-security.md)
* [网络](networking.md)
* [可支持性](supportability.md)
* [监视](monitoring.md)
