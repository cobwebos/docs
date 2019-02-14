---
title: 影响 Azure AD Connect 性能的因素
description: 本文档介绍多种因素对 Azure AD Connect 预配引擎的影响。 这些因素可帮助组织规划 Azure AD Connect 部署，确保满足其同步要求。
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a3a57fbe5df690e4dbdba8cbab85e62648bb298
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192568"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>影响 Azure AD Connect 性能的因素

Azure AD Connect 将 Active Directory 同步到 Azure AD。 此服务器是将用户标识迁移到云的过程中发挥着重要作用。 影响 Azure AD Connect 性能的主要因素包括：

| **设计因素**| **定义** |
|:-|-|
| 拓扑| Azure AD Connect 必须在网络上管理的终结点和组件的分布。 |
| 缩放| 要由 Azure AD Connect 管理的用户、组和 OU 等对象的数量。 |
| 硬件| 用于 Azure AD Connect 的硬件（物理或虚拟）以及各个硬件组件（包括 CPU、内存、网络和硬盘配置）的相关性能容量。 |
| 配置| Azure AD Connect 处理目录和信息的方式。 |
| 加载| 对象更改的频率。 一小时、一日或一周内的负载各不相同。 可能需要根据峰值负载或平均负载进行设计，具体取决于组件。 |

本文档旨在介绍影响 Azure AD Connect 预配引擎性能的因素。 大规模或复杂的组织（预配超过 10 万个对象的组织）如果遇到此处所述的任何性能问题，可使用建议方法来优化其 Azure AD Connect 实现。 其他 Azure AD Connect 组件（例如 [Azure AD Connect Health](how-to-connect-health-agent-install.md)）以及代理不在本文讨论范围之内。

> [!IMPORTANT]
> Microsoft 不支持通过未正式记录的方法修改或操作 Azure AD Connect。 其中的任何操作都可能会导致 Azure AD Connect 同步出现不一致或不受支持状态。因此，Microsoft 无法提供这种部署的技术支持。

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect 组件因素

下图显示了连接到一个林（尽管支持多个林）的预配引擎的高级体系结构。 此体系结构展示各组件之间的相互交互。

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

预配引擎连接到每个 Active Directory 林且连接到 Azure AD。 从每个目录读取信息的过程称为“导入”。 导出是指从预配引擎更新目录。 同步则评估规定对象在预配引擎内的流动方式的规则。 若要深入了解，可参阅 [Azure AD Connect 同步：了解体系结构](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)。

Azure AD Connect 使用以下临时区域、规则和过程，以实现从 Active Directory 到 Azure AD 的同步：

* **连接器空间 (CS)** - 在此暂存每个已连接目录（缩写为 CD，是实际目录）的对象，然后再由预配引擎处理它们。 Azure AD 具有自己的 CS，并且连接的每个林也都有自己的 CS。
* **Metaverse (MV)** - 在此根据同步规则创建需要同步的对象。 对象必须存在于 MV 中，然后它们才能将对象和属性填充到其他连接的目录。 只有一个 MV。
* **同步规则** - 决定将创建（投射）哪些对象或将哪些对象连接（联接）到 MV 中的对象。 还决定要从目录或向目录复制或转换的属性值。
* **运行配置文件** - 根据暂存区域和已连接目录之间的同步规则，捆绑复制对象及其属性值的过程步骤。

有多种用于优化预配引擎性能的运行配置文件。 大多数组织使用默认的计划和运行配置文件来处理常规操作，但某些组织可能需要[更改计划](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler)或触发其他运行配置文件以应对不常见的情况。 可用的运行配置文件如下：

### <a name="initial-sync-profile"></a>初始同步配置文件

初始同步配置文件涉及首次读取已连接目录（如 Active Directory 林）的过程。 此后，它将对同步引擎数据库中的所有项进行分析。 初始周期将在 Azure AD 中创建新对象；如果 Active Directory 林较大，将需要更多时间才能完成循环。 初始同步包括以下步骤：

1. 在所有连接器上完全导入
2. 在所有连接器上完全同步
3. 在所有连接器上导出

### <a name="delta-sync-profile"></a>增量同步配置文件

为优化同步过程，此运行配置文件仅处理已连接目录中在上次同步过程后发生的对象更改（创建、删除和更新）。 默认情况下，增量同步配置文件每 30 分钟运行一次。 组织应尽量将时间保持在 30 分钟以内，确保 Azure AD 保持最新状态。 要监视 Azure AD Connect 的运行状况，请使用[运行状况监视代理](how-to-connect-health-sync.md)查看过程中是否存在任何问题。 增量同步配置文件包括以下步骤：

1. 在所有连接器上增量导入
2. 在所有连接器上增量同步
3. 在所有连接器上导出

典型的企业组织增量同步方案是：

- 删除约 1% 的对象
- 创建约 1% 的对象
- 修改约 5% 的对象

变化率可能因组织更新 Active Directory 中用户的频率而异。 例如，聘用和解聘员工的高峰期可能出现较高的变化率。

### <a name="full-sync-profile"></a>完全同步配置文件

如果进行了以下任一项配置更改，则需要完全同步周期：



- 增加要从已连接目录导入的对象或属性的范围。 例如，向导入范围添加域或 OU。
- 更改同步规则。 例如，创建了新规则，用于从 Active Directory 中的 extension_attribute3 填充 Azure AD 中的用户职务。 此更新需要预配引擎重新检查所有现有用户是否已更新其职务，以便以后应用此更改。

完全同步周期包括以下操作：

1. 在所有连接器上完全导入
2. 在所有连接器上完全/增量同步
3. 在所有连接器上导出

> [!NOTE]
> 对 Active Directory 或 Azure AD 中的许多对象执行批量更新时，需要仔细规划。 批量更新将导致增量同步过程在导入时花费更长时间，因为有大量对象发生了更改。 即使批量更新未影响同步过程，也可能出现导入时间长的情况。 例如，将许可证分配给 Azure AD 中的多个用户将导致从 Azure AD 导入的周期较长，但不会导致 Active Directory 中的任何属性发生更改。

### <a name="synchronization"></a>同步

同步过程运行时具有以下性能特征：

* 同步是单线程的，因此，预配引擎不会并行处理已连接目录、对象或属性的运行配置文件。
* 导入时间随同步的对象数呈线性增长。 例如，如果导入 1 万个对象需要 10 分钟，则在同一服务器上导入 2 万个对象大约需要 20 分钟。
* 导出也是线性的。
* 同步将基于引用其他对象的对象的数量呈指数级增加。 组成员身份和嵌套组具有严重的性能影响，因为其成员引用用户对象或其他组。 要想完成同步周期，必须找到这些引用并使其引用到 MV 中的实际对象。

### <a name="filtering"></a>筛选

要导入的 Active Directory 拓扑的大小是影响性能和预配引擎内部组件所需总体时间的首要因素。

应利用[筛选](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering)减少要同步的对象。 这将避免处理不必要的对象并将其导出到 Azure AD。 可用的筛选方法如下（按优先顺序）：



- **基于域的筛选** - 使用此选项选择要同步到 Azure AD 的特定域。 在安装 Azure AD Connect 同步之后对本地基础结构进行更改时，必须在同步引擎配置中添加和删除域。
- **组织单位 (OU) 筛选** - 使用 OU 来确定要将 Active Directory 域中的哪些特定对象预配到 Azure AD。 在建议的筛选机制中，OU 筛选排在第二位，因为它使用简单的 LDAP 范围查询从 Active Directory 导入较小的对象子集。
- **按对象筛选属性** - 使用对象上的属性值确定是否在 Azure AD 中预配 Active Directory 中的特定对象。 当域和 OU 筛选无法满足特定筛选要求时，属性筛选非常适合用于微调筛选器。 属性筛选不会缩短导入时间，但会缩短同步和导出时间。
- **基于组的筛选** - 使用组成员身份确定是否应在 Azure AD 中预配对象。 基于组的筛选仅适用于测试场景，不建议用于生产场景，因为在同步周期期间检查组成员身份需要额外的开销。

Active Directory CS 中存在大量持久的[断开连接器对象](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects)，可能导致同步时间较长，因为在同步周期中，预配引擎必须重新评估每个断开连接器对象可能具有的连接。 要解决此问题，请考虑以下建议之一：



- 将断开连接器对象放置在使用域或 OU 筛选的导入的范围之外。
- 将对象投射/联接到 MV，并将 [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) 属性设置为 True，以免在 Azure AD CS 中预配这些对象。

> [!NOTE]
> 筛选出过多对象时，可能会令用户感到困惑，也可能出现应用程序权限问题。 例如，在混合 Exchange Online 实现中，使用本地邮箱的用户在全局地址列表中看到的用户比使用 Exchange Online 邮箱的用户看到的用户多。 在其他情况下，用户可能希望向不在筛选的对象集范围内的其他用户授予云应用中的访问权限。

### <a name="attribute-flows"></a>属性流

属性流是将对象的属性值从一个已连接目录复制或转换到另一个已连接目录的过程。 属性流属于同步规则。 例如，Active Directory 中用户的电话号码变化时，系统会更新 Azure AD 中的电话号码。 组织可以[修改属性流](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration)以满足各种要求。 建议先复制现有属性流，然后再执行更改。

简单重定向（例如将属性值提供给其他属性）不会产生重大性能影响。 将 Active Directory 中的移动电话号码作为 Azure AD 中的办公室电话号码，这就是一个重定向示例。

转换属性值可能对同步过程产生性能影响。 转换属性值包含对属性值进行修改、重设格式、连接或相减等操作。

组织可以阻止某些属性流入 Azure AD，但这不会影响预配引擎的性能。

> [!NOTE]
> 不要删除同步规则中不需要的属性流。 建议禁用这些属性流，因为 Azure AD Connect 升级期间会重新创建已删除的规则。

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect 依赖项因素

Azure AD Connect 的性能依赖于它导入和导出到的已连接目录的性能。 例如，它需要导入的 Active Directory 的大小或 Azure AD 服务的网络延迟。 预配引擎使用的 SQL 数据库也会影响同步周期的整体性能。

### <a name="active-directory-factors"></a>Active Directory 因素

如上文所述，要导入的对象数对性能具有显著影响。 [Azure AD Connect 的硬件和先决条件](how-to-connect-install-prerequisites.md)中概述了基于部署规模的特定硬件层。 Azure AD Connect 仅支持 [Azure AD Connect 的拓扑](plan-connect-topologies.md)中所述的特定拓扑。 对于不支持的拓扑，没有性能优化和建议。

基于要导入的 Active Directory 的大小，确保 Azure AD Connect 服务器满足硬件要求。 Azure AD Connect 服务器与 Active Directory 域控制器之间的错误或缓慢网络连接可能导致导入速度缓慢。

### <a name="azure-ad-factors"></a>Azure AD 因素

Azure AD 使用限制来防止云服务受到拒绝服务 (DoS) 攻击。 目前，Azure AD 限制为每 5 分钟 7,000 次写入（每小时 84,000 次）。 例如，可以限制以下操作：



- Azure AD Connect 向 Azure AD 的导出。
- PowerShell 脚本或应用程序直接更新、甚至在后台更新 Azure AD，如动态组成员资格。
- 用户更新自己的标识记录，例如注册 MFA 或 SSPR（自助式密码重置）。
- 图形用户界面中的操作。

规划部署和维护任务，确保 Azure AD Connect 同步周期不会受到限制的影响。 例如，如果在聘用高峰期间创建了数千个用户标识，可能会导致动态组成员资格、许可分配和自助式密码重置注册发生更新。 最好是将这些写入分配在数小时或数天内。

### <a name="sql-database-factors"></a>SQL 数据库因素

源 Active Directory 拓扑的大小将影响 SQL 数据库性能。 请遵循 SQL Server 数据库的[硬件要求](how-to-connect-install-prerequisites.md)并考虑以下建议：



- 拥有 10 万名以上用户的组织，可通过使 SQL 数据库和预配引擎位于同一服务器来减少网络延迟。
- 鉴于同步过程的磁盘输入和输出 (I/O) 要求高，为获得最佳结果，请对预配引擎的 SQL 数据库使用固态硬盘 (SSD)；如果无法使用，则考虑使用 RAID 0 或 RAID 1 配置。
- 请勿提前执行完全同步，这会造成不必要的改动和增加响应时间。

## <a name="conclusion"></a>结束语

要想优化 Azure AD Connect 实现的性能，请考虑以下建议：



- 基于 Azure AD Connect 服务器的实现大小，使用[推荐的硬件配置](how-to-connect-install-prerequisites.md)。
- 在大规模部署中升级 Azure AD Connect 时，考虑使用[交叉迁移方法](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration)，确保故障时间最短和可靠性最佳。 
- 对 SQL 数据库使用 SSD，实现最佳写入性能。
- 使用域、OU 或属性筛选来筛选 Active Directory 范围，使其只包含需要在 Azure AD 中预配的对象。
- 如果需要更改默认属性流规则，请先复制规则，再更改副本并禁用原始规则。 务必重新运行完全同步。
- 为初始完全同步运行配置文件规划充足的时间。
- 尽量在 30 分钟内完成增量同步周期。 如果未在 30 分钟内完成增量同步配置文件，请修改默认同步频率以确保包括一个完整的增量同步周期。
- 在 Azure AD 中监视 [Azure AD Connect 同步运行状况](how-to-connect-health-agent-install.md)。

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
