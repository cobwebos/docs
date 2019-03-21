---
title: Azure NetApp 文件的资源限制 | Microsoft Docs
description: 介绍 Azure NetApp 文件资源的限制，包括 NetApp 帐户、容量池、卷、快照和委派子网的限制。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 897ca26bcbb05287d33a4fb8e731ca959e39e271
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56668152"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp 文件的资源限制

了解 Azure NetApp 文件的资源限制，可帮助你管理卷。

- 每个 Azure 订阅最多可以有 10 个 NetApp 帐户。
- 每个 NetApp 帐户最多可以有 25 个容量池。
- 每个容量池可以仅属于一个 NetApp 帐户。  
- 单个容量池的最小大小为 4 TiB，最大大小为 500 TiB。 
- 每个容量池最多可以有 500 个卷。
- 单个卷的最小大小为 100 GiB，最大大小为 92 TiB。
- 每个卷最多可以有 255 个快照。
- 每个 Azure 虚拟网络 (Vnet) 只能将一个子网委派给 Azure NetApp 文件。

**后续步骤**

[了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
