---
title: Azure 安全中心的 IoT 必备组件 |Microsoft Docs
description: Azure 安全中心入门所需的所有内容的详细信息。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: mlottner
ms.openlocfilehash: cc2dc3a190e3ad06bdc048f2a5770eae2a6990ec
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596829"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>用于 IoT 必备组件的 Azure 安全中心

本文介绍了用于 IoT 服务的 Azure 安全中心 (ASC) 的不同构建基块、需要开始的内容, 并说明了有助于了解服务的基本概念。 

## <a name="minimum-requirements"></a>最低要求

- IoT 中心标准层
    - RBAC 角色**所有者**级别权限 
- [Log Analytics 工作区](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure 安全中心 (推荐)
    - 使用 Azure 安全中心是一种建议, 不是必需的。 如果没有 Azure 安全中心, 你将无法在 IoT 中心内查看其他 Azure 资源。 
 
## <a name="working-with-asc-for-iot-service"></a>使用适用于 IoT 的 ASC 服务

使用 Azure IoT 中心和 Azure 安全中心可获取 IoT insights 和报告的 ASC。 若要在 Azure IoT 中心为 IoT 启用 ASC, 需要具有**所有者**级别权限的帐户。 在 IoT 中心为 iot 启用 ASC 后, 将在 Azure IoT 中心中将 ASC 用于 iot insights, 并在 Azure 安全中心中显示为**iot** 。 

## <a name="supported-service-regions"></a>支持的服务区域 

以下 Azure 区域中的 IoT 中心目前支持用于 IoT 的 ASC:
  - 美国中部  
  - East US 
  - 美国东部 2
  - 美国中西部
  - 美国西部
  - 美国西部 2
  - 美国中部南部
  - 美国中北部
  - 加拿大中部
  - 加拿大东部 
  - 北欧    
  - 巴西南部
  - 法国中部  
  - 英国西部 
  - 英国南部
  - 西欧 
  - 北欧 
  - 日本西部  
  - 日本东部  
  - 澳大利亚东南部
  - 澳大利亚东部
  - 东亚   
  - 东南亚
  - 韩国中部
  - 韩国 
  - 印度中部
  - 印度南部
  
## <a name="wheres-my-iot-hub"></a>我的 IoT 中心在哪里？

在开始之前, 请查看 IoT 中心位置以验证服务可用性。 

1. 打开 IoT 中心。 
2. 单击“概览”。 
3. 验证列出的位置是否与其中一个[受支持的服务区域](#supported-service-regions)匹配。 


## <a name="supported-platforms-for-agents"></a>支持的代理平台 

用于 IoT 代理的 ASC 支持不断增长的设备和平台列表。 请参阅[支持的平台列表](how-to-deploy-agent.md), 查看现有或计划的设备库。  

## <a name="next-steps"></a>后续步骤
- 阅读 Azure IoT 安全[概述](overview.md)
- 了解如何[启用该服务](quickstart-onboard-iot-hub.md)
- 阅读[Azure 安全中心以获取 IOT 常见问题](resources-frequently-asked-questions.md)
- 了解如何[了解 IoT 警报的 Azure 安全中心](concept-security-alerts.md)
