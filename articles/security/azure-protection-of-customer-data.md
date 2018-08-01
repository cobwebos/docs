---
title: Azure 中客户数据的保护
description: 本文介绍 Azure 如何保护客户数据。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 0b702cec6113e6b31e34750872479dce162e4cb6
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173061"
---
# <a name="azure-customer-data-protection"></a>Azure 客户数据保护   
默认情况下，拒绝 Microsoft 运营和支持人员访问客户数据。 授予对客户数据的访问权限后，需要经过领导批准，并仔细管理和记录访问活动。 访问控制要求由以下 Azure 安全策略制定：

- 默认情况下无权访问客户数据。
- 客户虚拟机 (VM) 上没有用户帐户或管理员帐户。
- 授予完成任务所需的最低特权；审核并记录访问权限请求。

Microsoft 为 Azure 支持人员分配独特的企业 Active Directory 帐户。 Azure 依赖于 Microsoft 信息技术 (MSIT) 管理的 Microsoft Corporate Active Directory 来控制对关键信息系统的访问。 要求执行多重身份验证，只从安全的控制台授予访问权限。

所有访问尝试受到监视，可以通过一组基本报告来显示。

## <a name="data-protection"></a>数据保护
Azure 按默认或者以客户选项的形式为客户提供可靠的数据安全性。

**数据分离**：Azure 是一项多租户服务，这意味着，多个客户的部署和 VM 存储在同一物理硬件上。 Azure 使用逻辑隔离将每个客户的数据互相分离开来。 分离提供多租户服务的缩放和经济优势，同时严格防止客户访问其他人的数据。

**静态数据保护**：客户负责确保按标准加密 Azure 中存储的数据。 Azure 提供各种加密功能，便于客户选择满足自己需求的最佳解决方案。 Azure Key Vault 可帮助客户轻松保持对密钥的控制，以便云应用程序和服务用于加密数据。 客户可以使用 Azure 磁盘加密来加密 VM。 Azure 存储服务加密可以加密客户存储帐户中的所有数据。

**传输中数据保护**：客户可为自己 VM 与最终用户之间的流量启用加密。 Azure 会保护传入或传出组件的数据，以及在内部传输的数据，例如两个虚拟网络之间传输的数据。 Azure 根据 CESG/NCSC 的建议，结合 2048 位 RSA/SHA256 加密密钥使用行业标准传输层安全性 (TLS) 1.2 或更高版本的协议来加密以下各方之间的通信：

- 客户与云。
- Azure 系统和数据中心之间的内部通信。

**加密**：作为确保数据保密性和完整性的最佳做法，客户可以部署存储中数据加密和传输中数据加密。 客户可以直截了当地将其 Azure 云服务配置为使用 SSL 保护来自 Internet 的通信，甚至是 Azure 托管 VM 之间的通信。

**数据冗余**：出现网络攻击或者数据中心遭到物理损坏时，Microsoft 可帮助确保数据受到保护。 客户可以选择：

- 出于合规或延迟方面的考虑使用国内存储。
- 出于安全或灾难恢复目的使用国外存储。

数据可在选定的地理区域中进行复制以实现冗余，但不会传输到此区域以外。 客户可以使用多个选项来复制数据，包括指定副本数量，以及复制数据中心的数量和位置。

创建存储帐户时，请选择以下复制选项之一：

- **本地冗余存储 (LRS)**：本地冗余存储保留数据的三个副本。 LRS 会在单个区域中的单个设施内复制三次。 LRS 可以保护数据免受普通的硬件故障损害，但无法保护数据免受单个设施故障的损害。
- **区域冗余存储 (ZRS)**：区域冗余存储保留数据的三个副本。 ZRS 在两到三个个设施之间复制三次，其持久性比 LRS 更高。 复制在单个区域中或者在两个区域之间进行。 ZRS 帮助在单个区域内确保数据持久保存。
- **异地冗余存储 (GRS)**：创建存储帐户时，默认会为该存储帐户启用异地冗余存储。 GRS 维护数据的六个副本。 使用 GRS 时，数据将在主要区域中复制三次。 数据还会在离主要区域数百英里的次要区域中复制三次，从而提供最高级别的持久性。 当主要区域发生故障时，Azure 存储会故障转移到次要区域。 GRS 帮助在两个不同的区域中确保数据持久保存。

**数据销毁**：当客户删除数据或离开 Azure 时，Microsoft 会在重复使用之前遵循严格的规则覆盖存储资源，并对已退役的硬件执行物理销毁。 在客户提出请求和合同终止时，Microsoft 会执行完全数据删除。

## <a name="customer-data-ownership"></a>客户数据所有权
Microsoft 不会检查、审批或监视客户在 Azure 中部署的应用程序。 此外，Microsoft 不知道客户选择在 Azure 中存储哪种类型的数据。 Microsoft 不会基于客户在 Azure 中输入的信息声索数据所有权。

## <a name="records-management"></a>记录管理
针对后端数据，Azure 已制定内部记录保留要求。 客户负责确定其自己的记录保留要求。 对于存储在 Azure 中的记录，客户需负责提取其数据，并根据自己指定的保留期在 Azure 的外部保留内容。

Azure 可让客户从产品中导出数据和审核报告。 导出内容保存在本地，并根据客户定义的保留期保留信息。

## <a name="electronic-discovery-e-discovery"></a>电子发现
Azure 客户在使用 Azure 服务时需负责遵守电子发现要求。 如果 Azure 客户必须保留其客户数据，可在本地导出并保存数据。 此外，客户可以请求从 Azure 客户支持部门导出其数据。 除了允许客户导出其数据以外，Azure 还会在内部展开广泛的日志记录和监视。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 网络体系结构](azure-infrastructure-network.md)
- [Azure 生产网络](azure-production-network.md)
- [Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产运营和管理](azure-infrastructure-operations.md)
- [Azure 基础结构监视](azure-infrastructure-monitoring.md)
- [Azure 基础结构完整性](azure-infrastructure-integrity.md)
