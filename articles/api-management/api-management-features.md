---
title: Azure API 管理层的基于功能的比较 | Microsoft Docs
description: 基于其提供的功能比较 API 管理层。 查看概述每个定价层中可用的主要功能的表。
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/15/2020
ms.author: apimpm
ms.openlocfilehash: eec913237ba978e52bb64fbd4c1f043a9214ffc8
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077841"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Azure API 管理层的基于功能的比较

每个 API 管理[定价层](https://aka.ms/apimpricing)都提供了一组不同的功能和每单位[容量](api-management-capacity.md)。 下表总结了每个层中提供的主要功能。 某些功能可能根据层以不同的方式工作或具有不同的能力。 在这种情况下，介绍这些功能的文档文章中指出了差异。

> [!IMPORTANT]
> 请注意，开发人员层适用于非生产用例和评估。 它不提供 SLA。

| 功能                                                                                      | 消耗 | 开发人员 | 基本 | 标准 | 高级 |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD 集成<sup>1</sup>                                                             | 否          | 是       | 否    | 是      | 是     |
| 虚拟网络 (VNet) 支持                                                               | 否          | 是       | 否    | 否       | 是     |
| 多区域部署                                                                      | 否          | 否        | 否    | 否       | 是     |
| 多个自定义域名                                                                 | 否          | 是        | 否    | 否       | 是     |
| 开发人员门户<sup>2</sup>                                                                 | 否          | 是       | 是   | 是      | 是     |
| 内置缓存                                                                               | 否          | 是       | 是   | 是      | 是     |
| 内置分析                                                                           | 否          | 是       | 是   | 是      | 是     |
| [自承载网关](self-hosted-gateway-overview.md)<sup>3</sup>                           | 否          | 是       | 否    | 否       | 是     |
| [TLS 设置](api-management-howto-manage-protocols-ciphers.md)                             | 是         | 是       | 是   | 是      | 是     |
| [外部缓存](./api-management-howto-cache-external.md)                                                    | 是         | 是       | 是   | 是      | 是     |
| [客户端证书身份验证](api-management-howto-mutual-certificates-for-clients.md) | 是         | 是       | 是   | 是      | 是     |
| [备份和还原](api-management-howto-disaster-recovery-backup-restore.md)               | 否          | 是       | 是   | 是      | 是     |
| [基于 Git 的管理](api-management-configuration-repository-git.md)                        | 否          | 是       | 是   | 是      | 是     |
| 直接管理 API                                                                        | 否          | 是       | 是   | 是      | 是     |
| Azure Monitor 日志和指标                                                               | 是         | 是       | 是   | 是      | 是     |
| 静态 IP                                                                                    | 否          | 是       | 是   | 是      | 是     |

<sup>1</sup> 允许使用 Azure AD（和 Azure AD B2C）作为标识提供者，以用于开发人员门户上的用户登录。<br/>
<sup>2</sup> 包括相关功能，例如用户、组、问题、应用程序和电子邮件模板以及通知。<br/>
<sup>3</sup> 在开发人员层中，自承载网关仅限于单个网关节点。<br/>