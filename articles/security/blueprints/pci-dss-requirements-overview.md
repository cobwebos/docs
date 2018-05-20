---
title: Azure 付款处理蓝图 - 高级概述
description: Azure 付款处理蓝图 - 客户责任矩阵（概述）
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 4c36f50b5c4ceba911003ec7a633dcab8724c6e0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="pci-dss-requirements---high-level-overview"></a>PCI DSS 要求 - 高级概述

支付卡行业数据安全标准 (PCI DSS) 的开发目的是鼓励提高和增强持卡人数据安全性并全面促进广泛采用一致的数据安全措施。 PCI DSS 提供旨在保护帐户数据的技术和操作要求基线。 PCI DSS 适用于支付卡处理中所涉及的所有实体，包括商家、处理程序、收买者、颁发者和服务提供商。 PCI DSS 也适用于存储、处理或传输持卡人数据 (CHD) 和/或敏感的身份验证数据 (SAD) 的所有其他实体。 下面是 12 个 PCI DSS 要求的高级概述。

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

|   |   |
|---|---|
| **生成并维护安全的<br/>网络和系统** | 1.[安装并维护防火墙配置，以保护持卡人数据](pci-dss-requirement-1-firewall.md)<br/><br/> 2.[不要将供应商提供的默认值用作系统密码和其他安全参数](pci-dss-requirement-2-password.md) |  
| **保护持卡人数据** | 3.[保护存储的持卡人数据](pci-dss-requirement-3-chd.md)<br/><br/> 4.[跨开放的公共网络加密传输持卡人数据](pci-dss-requirement-4-encryption.md) |
| **维护漏洞<br/>管理程序** | 5.[保护所有系统免受恶意软件威胁，并定期更新防病毒软件或程序](pci-dss-requirement-5-malware.md)<br/><br/> 6.[开发并维护安全系统和应用程序](pci-dss-requirement-6-secure-system.md) |
| **实现强访问<br/>控制措施** | 7.[按需要知道的业务限制对持卡人数据的访问](pci-dss-requirement-7-access.md)<br/><br/> 8.[标识并验证对系统组件的访问](pci-dss-requirement-8-identity.md) <br/><br/> 9.[限制对持卡人数据的物理访问](pci-dss-requirement-9-physical-access.md) |
| **定期监视和<br/>测试网络** | 10.[跟踪并监视对网络资源和持卡人数据的所有访问](pci-dss-requirement-10-monitoring.md) <br/><br/> 11.[定期测试安全系统和进程](pci-dss-requirement-11-testing.md) |
| **维护信息<br/>安全策略** | 12.[维护用于解决所有人员的信息安全性问题的策略](pci-dss-requirement-12-policy.md) |

