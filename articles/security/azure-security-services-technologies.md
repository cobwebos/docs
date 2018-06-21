---
title: Azure 安全服务与技术 | Microsoft Docs
description: 本文提供了一个包含 Azure 安全服务与技术的特选列表。
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2018
ms.author: barclayn
ms.openlocfilehash: e52cee2cb642de6e54270c597e6ed99f7162d0ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641452"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Azure 上可用的安全服务和技术

在我们与当前和未来 Azure 客户的讨论中，我们经常被问及“你们是否有 Azure 必须提供的所有安全相关服务和技术的列表？”

了解以下信息有助于评估云服务提供程序选项。 因此，我们提供此列表帮助你入门。

随着时间推移，此列表会像 Azure 那样不断增长变化。 请务必定期检查此页面，掌握我们最新的与安全相关的服务和技术。

## <a name="general-azure-security"></a>Azure 常规安全性
|服务|说明|
|--------|--------|
|[Azure&nbsp;安全&nbsp;Center](../security-center/security-center-intro.md)| 一个云工作负荷保护解决方案，可跨混合云工作负荷提供安全性管理和高级威胁防护。|
|[Azure Key Vault](../key-vault/key-vault-overview.md)| 一个安全的机密存储空间，用于存储密码、连接字符串和维持应用正常工作所需的其他信息。 |
|[Log Analytics](../log-analytics/log-analytics-overview.md)|一项监视服务，它收集遥测和其他数据，并且提供查询语言和分析引擎，以传递应用和资源操作见解。 可单独使用或与其他服务一同使用（例如安全中心）。 |
|[Azure 开发/测试实验室](../devtest-lab/devtest-lab-overview.md)|一项可帮助开发人员和测试人员在 Azure 中快速创建环境，同时尽量减少浪费并控制成本的服务。  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>存储安全
|服务|说明|
|------|--------|
| [Azure&nbsp;存储&nbsp;服务&nbsp;加密](../storage/common/storage-service-encryption.md)|一项安全功能，会自动加密 Azure 存储中的数据。   |
|[StorSimple 加密混合存储](../storsimple/storsimple-ova-overview.md)| 一种用于管理本地设备与 Azure 云存储之间的存储任务的集成存储解决方案。|
|[Azure 客户端加密](../storage/common/storage-client-side-encryption.md)| 一个客户端加密解决方案，它在将数据上传到 Azure 存储前在客户端应用程序内加密数据，并在下载时解密数据。 |
| [Azure 存储共享访问签名](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|共享访问签名对存储帐户中的资源提供委托访问。  |
|[Azure 存储帐户密钥](../storage/common/storage-create-storage-account.md)| 一种适用于 Azure 存储的访问控制方法，用于在访问存储帐户时进行身份验证。 |
|[带有 SMB 3.0 加密的 Azure 文件共享](../storage/files/storage-files-introduction.md)|一项网络安全技术，它为服务器消息块 (SMB) 文件共享协议启用自动网络加密。 |
|[Azure 存储分析](https://docs.microsoft.com/en-us/rest/api/storageservices/Storage-Analytics)| 一项记录和指标生成技术，适用于存储帐户中的数据。 |

<!------>

## <a name="database-security"></a>数据库安全
|服务|说明|
|------|--------|
| [Azure&nbsp;SQL&nbsp;防火墙](../sql-database/sql-database-firewall-configure.md)|一项网络访问控制功能，对针对数据库的网络攻击进行防护。 |
|[Azure&nbsp;SQL&nbsp;单元格&nbsp;级别加密](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| 一种提供粒度级别加密的数据库安全技术。  |
| [Azure&nbsp;SQL&nbsp;连接加密](../sql-database/sql-database-control-access.md)|为了提供安全性，SQL 数据库会使用按 IP 限制连接的防火墙规则、要求用户证明其身份的身份验证机制，以及只允许用户执行特定操作和访问特定数据的授权机制，来控制访问。 |
| [Azure SQL 始终加密](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|保护 Azure SQL 数据库或 SQL Server 数据库中存储的敏感数据，如信用卡号或国民身份证号（例如，美国社会安全号码）。  |
| [Azure&nbsp;SQL&nbsp;透明数据加密](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| 一种加密整个数据库存储的数据库安全功能。 |
| [Azure SQL 数据库审核](../sql-database/sql-database-auditing.md)|一种跟踪数据库事件并将事件写入 Azure 存储帐户中的审核日志的数据库审核功能。  |


## <a name="identity-and-access-management"></a>标识和访问管理
|服务|说明|
|------|--------|
| [Azure&nbsp;角色&nbsp;基于&nbsp;访问控制](../active-directory/role-based-access-control-configure.md)|一项访问控制功能，它基于用户在组织内的角色，仅允许用户访问其必须访问的内容。  |
| [Azure Active Directory](../active-directory/active-directory-whatis.md)|一个基于云的身份验证存储库，它支持基于云的多租户目录和 Azure 中的多标识管理服务。  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|一项标识管理服务，帮助控制客户使用基于 Azure 的应用程序时如何注册、登录和管理其配置文件。   |
| [Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-overview.md)| Active Directory 域服务基于云的托管版本。 |
| [Azure 多重身份验证](../active-directory/authentication/multi-factor-authentication.md)| 一项安全性设置，它会采用几种形式的身份验证和验证，再允许访问安全信息。 |

## <a name="backup-and-disaster-recovery"></a>备份和灾难恢复
|服务|说明|
|------|--------|
| [Azure&nbsp;备份](../backup/backup-introduction-to-azure-backup.md)| 一项基于 Azure 的服务，用于备份和还原 Azure 云中的数据。 |
| [Azure&nbsp;Site&nbsp;Recovery](../site-recovery/site-recovery-overview.md)|一项联机服务，它可将在物理计算机和虚拟机 (VM) 上运行的工作负荷从主站点复制到辅助位置，以便在出现故障后恢复服务。 |

## <a name="networking"></a>网络
|服务|说明|
|------|--------|
| [网络&nbsp;安全&nbsp;组](../virtual-network/virtual-networks-nsg.md)| 一项基于网络的访问控制功能，它使用 5 元组进行允许或拒绝决策。  |
| [Azure VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)| 一种网络设备，用作 VPN 终结点，以允许跨界访问 Azure 虚拟网络。  |
| [Azure 应用程序网关](../application-gateway/application-gateway-introduction.md)|高级 Web 应用程序负载均衡器，可基于 URL 进行路由并执行 SSL 卸载。 |
| [Azure 负载均衡器](../load-balancer/load-balancer-overview.md)|TCP/UDP 应用程序网络负载均衡器。 |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| 本地网络和 Azure 虚拟网络之间的专用 WAN 链接。 |
| [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)| 一种全局 DNS 负载均衡器。|
| [Azure 应用程序代理](../active-directory/active-directory-application-proxy-get-started.md)| 用于保护远程访问本地托管 Web 应用程序的身份验证前端。 |