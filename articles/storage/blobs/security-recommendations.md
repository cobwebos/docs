---
title: Blob 存储的安全建议
titleSuffix: Azure Storage
description: 了解有关 Blob 存储的安全建议。 实施本指南可帮助你满足我们的共享责任模型中所述的安全要求。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: ee96ec85869cbde9d5fac540e9fbbaac88858daf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479379"
---
# <a name="security-recommendations-for-blob-storage"></a>Blob 存储的安全建议

本文包含对 Blob 存储的安全建议。 实施这些建议可帮助你满足我们的共享责任模型中所述的安全要求。 若要详细了解 Microsoft 如何满足服务提供商的责任，请阅读[云计算的共享责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)。

Azure 安全中心会自动监视本文中包含的一些建议。 Azure 安全中心是保护 Azure 中资源的第一道防线。 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../../security-center/security-center-intro.md)。

Azure 安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向您提供有关如何解决这些问题的建议。 有关 Azure 安全中心建议的详细信息，请参阅[Azure 安全中心中的安全建议](../../security-center/security-center-recommendations.md)。

## <a name="data-protection"></a>数据保护

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 使用 Azure 资源管理器部署模型 | 使用 Azure 资源管理器部署模型创建新的存储帐户以实现重要的安全增强功能，其中包括高级访问控制（RBAC）和审核、基于资源管理器的部署和监管、访问托管标识、访问为机密 Azure Key Vault，以及用于访问 Azure 存储数据和资源的基于 Azure AD 的身份验证和授权。 如果可能，请迁移使用经典部署模型的现有存储帐户以使用 Azure 资源管理器。 有关 Azure 资源管理器的详细信息，请参阅[azure 资源管理器概述](/azure/azure-resource-manager/resource-group-overview)。 | - |
| 在所有存储帐户上启用 "**需要安全传输**" 选项 | 如果启用 "**需要安全传输**" 选项，则对存储帐户发出的所有请求都必须在安全连接上进行。 通过 HTTP 发出的任何请求都将失败。 有关详细信息，请参阅[在 Azure 存储中要求安全传输](../common/storage-require-secure-transfer.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 为所有存储帐户启用高级威胁防护 | 适用于 Azure 存储的高级威胁防护提供额外的安全智能层，用于检测访问或利用存储帐户的异常和潜在有害尝试。 如果活动发生异常，则会在 Azure 安全中心触发安全警报，并通过电子邮件发送给订阅管理员，并详细介绍可疑活动以及如何调查和修正威胁的建议。 有关详细信息，请参阅[Azure 存储的高级威胁防护](../common/storage-advanced-threat-protection.md)。 | [是](../../security-center/security-center-sql-service-recommendations.md) |
| 为 blob 数据启用软删除 | 软删除使你能够在删除 blob 数据后将其恢复。 有关软删除的详细信息，请参阅[Azure 存储 blob 的软删除](storage-blob-soft-delete.md)。 | - |
| 在不可变 blob 中存储业务关键数据 | 配置法律持有和基于时间的保留策略，以将 blob 数据存储在蠕虫（写入一次，读取多个）状态。 可以读取存储 immutably 的 blob，但不能在保留间隔期间对其进行修改或删除。 有关详细信息，请参阅[将业务关键 blob 数据存储在不可变的存储](storage-blob-immutable-storage.md)中。 | - |
| 仅限 HTTPS 连接的共享访问签名（SAS）令牌 | 当客户端使用 SAS 令牌访问 blob 数据时，需要使用 HTTPS 有助于最大程度地降低窃听的风险。 有关详细信息，请参阅[使用共享访问签名（SAS）授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。 | - |

## <a name="identity-and-access-management"></a>身份和访问管理

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 使用 Azure Active Directory （Azure AD）授权对 blob 数据的访问 | Azure AD 通过共享密钥提供高级安全和易用性，以将请求授权给 Blob 存储。 有关详细信息，请参阅[使用 Azure Active Directory 授予对 Azure blob 和队列的访问权限](../common/storage-auth-aad.md)。 | - |
| 通过 RBAC 向 Azure AD 安全主体分配权限时，请记住最低权限的原则 | 将角色分配给用户、组或应用程序时，只向该安全主体授予执行任务所需的权限。 限制对资源的访问有助于防止意外和恶意地滥用数据。 | - |
| 使用用户委托 SAS 授予客户端对 blob 数据的有限访问权限 | 用户委托 SAS 使用 Azure Active Directory （Azure AD）凭据以及为 SAS 指定的权限进行保护。 用户委托 SAS 在其作用域和功能方面类似于服务 SAS，但通过服务 SAS 提供了安全性优势。 有关详细信息，请参阅[使用共享访问签名（SAS）授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 | - |
| 使用 Azure Key Vault 保护帐户访问密钥 | Microsoft 建议使用 Azure AD 对 Azure 存储空间的请求进行授权。 但是，如果必须使用共享密钥授权，请使用 Azure Key Vault 保护帐户密钥。 可以在运行时从密钥保管库检索密钥，而不是将其与应用程序一起保存。 有关 Azure Key Vault 的详细信息，请参阅[Azure Key Vault 概述](../../key-vault/key-vault-overview.md)。 | - |
| 定期重新生成帐户密钥 | 定期轮换帐户密钥可以降低向恶意执行组件公开数据的风险。 | - |
| 向 SAS 分配权限时，请记住最低权限的原则 | 创建 SAS 时，请仅指定客户端执行其功能所需的那些权限。 限制对资源的访问有助于防止意外和恶意地滥用数据。 | - |
| 为颁发给客户端的任何 SAS 准备好吊销计划 | 如果 SAS 遭到入侵，你需要尽快吊销该 SAS。 若要吊销用户委托 SAS，请撤销用户委托密钥，以快速使与该密钥关联的所有签名失效。 若要撤消与存储访问策略关联的服务 SAS，你可以删除该存储访问策略，重命名该策略，或者将其到期时间更改为过去的时间。 有关详细信息，请参阅[使用共享访问签名（SAS）授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。  | - |
| 如果服务 SAS 不与存储访问策略相关联，则将过期时间设置为1小时或更短 | 不能撤销与存储访问策略无关的服务 SAS。 出于此原因，建议限制过期时间，使 SAS 的有效时间为一小时或更短。 | - |
| 限制对容器和 blob 的匿名公共读取访问 | 对容器及其 blob 的匿名公共读取访问权限向任何客户端授予对这些资源的只读访问权限。 除非方案需要，否则请避免启用公共读取访问。 | - |

## <a name="networking"></a>联网

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 启用防火墙规则 | 配置防火墙规则，以将对存储帐户的访问限制为源自指定 IP 地址或范围的请求，或限制为来自 Azure 虚拟网络（VNet）中的子网列表的请求。 有关配置防火墙规则的详细信息，请参阅[Azure 文件同步代理和防火墙设置](../files/storage-sync-files-firewall-and-proxy.md)。 | - |
| 允许受信任的 Microsoft 服务访问存储帐户 | 默认情况下，打开存储帐户的防火墙规则会阻止传入的数据请求，除非这些请求源自 Azure 虚拟网络（VNet）或允许的公共 IP 地址中的服务操作。 被阻止的请求包括来自其他 Azure 服务、来自 Azure 门户、来自日志记录和指标服务等的请求。 可以通过添加例外，允许受信任的 Microsoft 服务访问存储帐户，从而允许来自其他 Azure 服务的请求。 有关为受信任的 Microsoft 服务添加例外的详细信息，请参阅[Azure 文件同步代理和防火墙设置](../files/storage-sync-files-firewall-and-proxy.md)。| - |
| 使用专用终结点 | 专用终结点将 Azure 虚拟网络（VNet）中的专用 IP 地址分配给存储帐户。 它通过专用链接保护 VNet 和存储帐户之间的所有流量。 有关专用终结点的详细信息，请参阅[使用 Azure 专用终结点将专用连接到存储帐户](../../private-link/create-private-endpoint-storage-portal.md)。 | - |
| 限制对特定网络的网络访问 | 将网络访问限制在托管要求访问的客户端的网络上，可减少资源暴露给网络攻击。 | [是](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>日志记录/监视

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 跟踪请求的授权方式 | 启用 Azure 存储日志记录，以跟踪对 Azure 存储的每个请求的授权方式。 日志通过使用 OAuth 2.0 令牌、使用共享密钥或使用共享访问签名（SAS）来指示请求是否是匿名的。 有关详细信息，请参阅[Azure 存储分析日志记录](../common/storage-analytics-logging.md)。 | - |

## <a name="next-steps"></a>后续步骤

- [Azure 安全文档](https://docs.microsoft.com//azure/security/)
- [安全开发文档](https://docs.microsoft.com/azure/security/develop/)。
