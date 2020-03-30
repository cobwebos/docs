---
title: 适用于队列存储的安全建议
titleSuffix: Azure Storage
description: 了解适用于队列存储的安全建议。 实施此指南将有助于你履行我们的共享职责模型中描述的安全职责。
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060897"
---
# <a name="security-recommendations-for-queue-storage"></a>适用于队列存储的安全建议

本文包含适用于队列存储的安全建议。 实施执行建议将有助于你履行我们的共享职责模型中描述的安全职责。 若要详细了解 Microsoft 采取哪些措施来履行服务提供商责任，请阅读[云计算的责任分担](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)。

包含在本文中的某些建议可能受 Azure 安全中心的自动监视。 在保护你在 Azure 中的资源方面，Azure 安全中心是第一道防线。 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../../security-center/security-center-intro.md)。

Azure 安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向你提供有关如何解决这些安全漏洞的建议。 有关 Azure 安全中心建议的详细信息，请参阅 [Azure 安全中心的安全性建议](../../security-center/security-center-recommendations.md)。

## <a name="data-protection"></a>数据保护

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 使用 Azure 资源管理器部署模型 | 使用 Azure 资源管理器部署模型创建新的存储帐户，以进行重要的安全增强，包括高级访问控制 (RBAC) 和审核、基于资源管理器的部署和治理、托管标识访问权限、用于提供机密的 Key Vault 的访问权限、用于访问 Azure 存储数据和资源的基于 Azure AD 的身份验证和授权。 如果可能，请迁移使用经典部署模型的现有存储帐户以使用 Azure 资源管理器。 有关 Azure 资源管理器的详细信息，请参阅 [Azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)。 | - |
| 在所有存储帐户中启用“需要安全传输”选项**** | 启用“需要安全传输”选项时，对存储帐户发出的所有请求都必须通过安全连接进行****。 通过 HTTP 发出的任何请求都将失败。 有关详细信息，请参阅[在 Azure 存储中要求安全传输](../common/storage-require-secure-transfer.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 为所有存储帐户启用高级威胁防护 | Azure 存储的高级威胁保护提供了额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 当活动异常发生时，在 Azure 安全中心中触发安全警报，并且也会通过电子邮件发送给订阅管理员，其中详细说明可疑活动以及如何调查和修复威胁的建议。 有关详细信息，请参阅[Azure 存储的高级威胁保护](../common/storage-advanced-threat-protection.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 将共享访问签名 (SAS) 令牌限制为仅用于 HTTPS 连接 | 当客户端使用 SAS 令牌访问队列数据时要求使用 HTTPS 有助于最大程度地降低被窃听的风险。 有关详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。 | - |

## <a name="identity-and-access-management"></a>标识和访问管理

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 使用 Azure Active Directory (Azure AD) 授权对队列数据的访问 | 与用于授权对队列存储的请求的共享密钥相比，Azure AD 提供了卓越的安全性和易用性。 有关详细信息，请参阅[使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限](../common/storage-auth-aad.md)。 | - |
| 通过 RBAC 向 Azure AD 安全主体分配权限时，请记住最低权限原则 | 将角色分配给用户、组或应用程序时，只向该安全主体授予执行任务所需的权限。 限制对资源的访问有助于防止意外和恶意滥用数据。 | - |
| 使用 Azure Key Vault 保护帐户访问密钥 | Microsoft 建议使用 Azure AD 对 Azure 存储的请求进行授权。 但是，如果必须使用共享密钥授权，请使用 Azure Key Vault 保护帐户密钥。 可以在运行时从密钥保管库检索密钥，而不是将其与应用程序一起保存。 | - |
| 定期重新生成帐户密钥 | 定期轮换帐户密钥可以降低向恶意参与者公开数据的风险。 | - |
| 向 SAS 分配权限时，请记住最低权限原则 | 创建 SAS 时，请仅指定客户端执行其功能所需的权限。 限制对资源的访问有助于防止意外和恶意滥用数据。 | - |
| 为发布给客户端的任何 SAS 制定吊销计划 | 如果 SAS 遭到泄露，需要尽快撤销该 SAS。 要撤销用户委托 SAS，请撤销用户委托密钥，以使与该密钥关联的所有签名快速失效。 要撤销与存储的访问策略关联的服务 SAS，可以删除存储的访问策略，重命名策略或将其到期时间更改为过去的时间。 有关详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。  | - |
| 如果服务 SAS 与存储的访问策略没有关联，请将到期时间设置为一小时或更短 | 无法撤销与存储的访问策略没有关联的服务 SAS。 因此，建议限制到期时间，以使 SAS 的有效时间不超过一小时。 | - |

## <a name="networking"></a>网络

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 启用防火墙规则 | 配置防火墙规则以将存储帐户的访问权限限制于源自指定的 IP 地址或范围，或源自 Azure 虚拟网络 (VNet) 中一系列子网的请求。 有关配置防火墙规则的详细信息，请参阅 Azure[文件同步代理和防火墙设置](../files/storage-sync-files-firewall-and-proxy.md)。 | - |
| 允许受信任的 Microsoft 服务访问此存储帐户 | 默认情况下，除非请求源自在 Azure 虚拟网络 (VNet) 中运行的服务或者源自允许的公共 IP 地址，否则启用存储帐户的防火墙规则会阻止数据传入请求。 被阻止的请求包括来自其他 Azure 服务、来自 Azure 门户、来自日志记录和指标服务等的请求。 可以通过添加例外，允许受信任的 Microsoft 服务访问此存储帐户，从而允许来自其他 Azure 服务的请求。 有关为受信任的 Microsoft 服务添加异常的详细信息，请参阅[Azure 文件同步代理和防火墙设置](../files/storage-sync-files-firewall-and-proxy.md)。| - |
| 使用专用终结点 | 专用终结点从 Azure 虚拟网络 （VNet） 向存储帐户分配专用 IP 地址。 它通过专用链路保护 VNet 和存储帐户之间的所有流量。 有关专用终结点的详细信息，请参阅[使用 Azure 专用终结点 将私人连接到存储帐户](../../private-link/create-private-endpoint-storage-portal.md)。 | - |
| 限制对特定网络的网络访问 | 限制对需要访问的托管客户端的网络的网络的访问，可以减少资源受到网络攻击的风险。 | [是](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>日志记录/监视

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 跟踪请求的授权方式 | 启用 Azure 存储日志记录以跟踪对 Azure 存储发出的每个请求的授权方式。 日志可指示请求是匿名提出的，还是使用 OAuth 2.0 令牌、共享密钥或共享访问签名 (SAS) 提出的。 有关详细信息，请参阅 [Azure 存储分析日志记录](../common/storage-analytics-logging.md)。 | - |

## <a name="next-steps"></a>后续步骤

- [Azure 安全文档](https://docs.microsoft.com//azure/security/)
- [安全开发文档](https://docs.microsoft.com/azure/security/develop/)。
