---
title: Azure 安全基准概述
description: 安全基准概述
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: c16d7247b781fea04cfa2d53b8854cff14e039c7
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616448"
---
# <a name="overview-of-azure-security-baselines"></a>Azure 安全基线概述

Azure 安全基线可帮助你通过改进的工具、跟踪和安全功能增强产品的安全性，并在保护环境时提供一致的体验。

Azure 服务安全基线侧重于以云为中心的控制领域。 这些控件与众所周知的安全性基准，如 Internet 安全性（CI）中心所述。 我们的基线为[Azure 安全基准](overview.md)中列出的控制区域提供指导。

每项建议都包含以下信息：
- **AZURE ID**：对应于建议的 Azure 安全基准 ID。
- **建议**：在 Azure ID 后直接执行以下操作，提供对控件的高级说明。
- **指导**：建议的基本原理，并链接到如何实现该建议。 如果 Azure 安全中心支持此建议，则还将列出这些信息。
- **责任**：负责实现控件的人员。 可能的方案是客户责任、Microsoft 责任或共享责任。
- **Azure 安全中心监视**： Azure 安全中心是否会监视该控件，并提供指向引用的链接。

所有建议（包括不适用于此特定服务的建议）都包含在基线中，以提供有关 Azure 安全基准的如何与每个服务相关的完整图。 有时可能会出于各种原因而不适用的控件，例如，IaaS/以计算为中心的控件（例如特定于 OS 配置管理的控件）可能不适用于 PaaS 服务。
