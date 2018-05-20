---
title: Azure 付款处理蓝图 - 加密要求
description: PCI DSS 要求 4
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6de3290fc2147e3c8ed63642b6e8470093898ef6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>符合 PCI DSS 的环境的加密要求 
## <a name="pci-dss-requirement-4"></a>PCI DSS 要求 4

**跨开放的公共网络加密传输持卡人数据**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

在通过容易被恶意个人访问的网络进行传输过程中必须加密敏感信息。 采用旧版加密和身份验证协议错误配置的无线网络和漏洞继续成为利用这些漏洞来获取对持卡人数据环境的特许访问权限的恶意个人的目标。

## <a name="pci-dss-requirement-41"></a>PCI DSS 要求 4.1

**4.1** 使用强加密和安全协议（例如，TLS、IPSEC、SSH 等）在通过开放的公共网络进行传输的过程中保护敏感的持卡人数据，包括以下方面：
- 仅接受受信任的密钥和证书。
- 正在使用的协议仅支持安全版本或配置。
- 加密强度适合于正在使用的加密方法。 

> [!NOTE]
> 使用 SSL/早期 TLS 时，必须完成附录 A2 中的要求。
>
> 开放的公共网络示例包括但不限于：
> - Internet
> - 无线技术，包括 802.11 和蓝牙
> - 移动电话技术，例如，全球移动通信系统 (GSM)、分码多址访问 (CDMA)
> - 通用分组无线业务 (GPRS)
> - 卫星通信


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 是一种针对部署实现强加密的 PaaS 解决方案，如下所示：<br /><br />为了满足加密静态数据的要求，[Azure 存储](https://azure.microsoft.com/services/storage/)使用以下项：<br /><br /><ul><li>[静态数据的 Azure 存储服务加密 (SSE)](/azure/storage/storage-service-encryption)</li><li>SQL 数据库：PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。</li><li>[Azure 磁盘加密 (Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>使用 Azure Key Vault 符合 Azure 政府、PCI DSS 和 HIPAA 要求。|



### <a name="pci-dss-requirement-411"></a>PCI DSS 要求 4.1.1

**4.1.1** 确保传输持卡人数据或连接到持卡人数据环境的无线网络使用行业最佳做法（例如，IEEE 802.11i）实现身份验证和传输的强加密。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 解决方案中未实现无线和 SNMP。|



## <a name="pci-dss-requirement-42"></a>PCI DSS 要求 4.2

**4.2** 永远不使用最终用户消息传送技术（例如，电子邮件、即时消息、短信、聊天，等等）发送未受保护的 PAN。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 不实现任何可能会发送未受保护的主帐号 (PAN) 数据的消息传送解决方案。|



## <a name="pci-dss-requirement-43"></a>PCI DSS 要求 4.3

**4.3** 确保用于加密传输持卡人数据的安全策略和操作过程都已记录在案、在使用中并对所有受影响方已知。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 客户负责记录和加密包含持卡人数据的传输。|




