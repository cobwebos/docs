---
title: Azure Data Box、Azure Data Box Heavy 事件的审核日志 |Microsoft Docs
description: 描述在 Azure Data Box 的各个阶段收集的 Data Box 的完整审核日志，并 Azure Data Box Heavy 订单。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209304"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 和 Azure Data Box Heavy 的审核日志

日志是一段时间内发生的离散事件的不可变、有时间戳的记录。 日志包含设备中的诊断、审核和安全信息。  

Data Box 或 Data Box Heavy 顺序在其操作过程中完成以下步骤：订单、设置、数据复制、返回、上传到 Azure 并验证和数据擦除。 对于上述每个步骤，将审核并记录所有事件。

本文包含有关 Data Box 审核日志的信息，其中包括日志类型和收集的信息以及日志的位置。 

本文中的信息同时适用于 Data Box 和 Data Box Heavy。 在后续部分，有关 Data Box 的任何参考信息也适用于 Data Box Heavy。 本文不介绍从 Azure 中运行的 Data Box 服务收集的日志。 


## <a name="about-audit-logs"></a>关于审核日志 

在 Data Box 上，收集以下日志：

- **系统日志** -Data Box 作为基于 Windows 的设备，将记录所有硬件、软件和系统事件。 系统审核日志中收集并报告了一组这些事件。 

- **安全** Data Box 成为基于 Windows 的设备，将记录所有安全事件。 在安全审核日志中收集和报告一组这些事件。 

- **应用程序** -这些日志仅特定于 Data Box。 这些日志包含设备上生成的所有事件，以响应运行的 Data Box 服务。

下一节将讨论其中的每个日志。

### <a name="system-logs"></a>系统日志

以下系统日志事件 Id 作为系统审核日志收集到 Data Box：

|事件提供程序名称     |收集的事件 ID   |事件说明   |
|-------------------|----------|----------------|
|Microsoft-Windows-内核-常规|12  |OS 重新启动时的 UTC 时间。   |
|                                |13  |关闭 OS 时的 UTC 时间。 |
|    |                              |
|Microsoft-内核-电源  |41  |系统在无干净关闭的情况下重新启动。| 
|    |                              |
|Microsoft-Windows-BitLocker-驱动程序|All|    |

### <a name="security-logs"></a>安全日志

以下安全日志事件 Id 作为 Data Box 上的安全审核日志收集：

|事件提供程序名称                   |收集的事件 ID    |事件说明       |
|--------------------------------------|------------|----------|
|Microsoft Windows 安全审核   |4624        |成功的登录。 |
|                                      |4625        |帐户登录失败。 未知的用户名或密码错误。 |
|                                     

### <a name="application-logs"></a>应用程序日志

以下应用程序日志事件 Id 作为 Data Box 上包审核日志的一部分收集。     

- **DataBox-审核** -包含本地 UI 中发生的事件。 
- **DataBox-重新设置-Audit** -包含与 Data Box 设备的重新设置相关的事件。 当通过本地 UI 重置设备时，将发生 Data Box 的重新设置。 如果要擦除已复制的数据，请选择此选项，方法是删除现有共享，然后重新创建共享作为重新设置或设备重置的一部分。
- **DataBox** --HcsMgmt-仅在设备退回到 Azure 数据中心之前，包含仅与 **准备** 发送步骤相关的事件。 
- **Microsoft-DataBox-IfxAudit** -包含有关作业的产品的不同实体记录的消息，这些消息指示有关某些流中发生的情况的详细信息。

下表汇总了各种事件提供程序以及在每种情况下收集的相应事件 Id。

|事件提供程序名称    |事件 ID    | 注意 |
|-----------------|-----------------|-------------------|
|Microsoft-DataBox-审核 |4624        |成功的登录。|
|                                      |4625        |帐户登录失败。 未知的用户名或密码错误。|
|                                     |4634        |注销事件。|
|                                   |  | |
|Microsoft-DataBox-重新设置-Audit    |65001       |成功的重新设置事件。|
|                                                  |65002       |未能重新设置事件。|
|                                                  |                 |         |
|Microsoft-DataBox-HcsMgmt-Audit        |65003       |准备交付状态事件 NotStarted、正在进行、失败、已取消、成功、ScanCompletedWithIssues、SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-DataBox-IfxAudit    |All |所有事件都记录在代码中的审核日志 API |

下面是 (INFINEON) 审核日志的检测框架示例：

|     任务/作业/API                              |     记录的事件                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     清理                                   |     记录与清除作业的开始、完成或失败相关的事件。 |                                              
|     为客户发货准备设备    |     记录用于为发货准备设备的作业开始、完成或失败相关事件。 |
|     预配                                 |     记录与设备预配作业的开始、完成或失败相关的事件。|
|     审核包作业                       |     记录与创建保管链日志链相关的审核包作业的开始、完成或失败事件。|
|     磁盘覆盖                          |     记录磁盘无法覆盖。|
|     启用或禁用远程 PowerShell     |     记录与启用或禁用设备上的远程 PowerShell 相关的事件。 |
|     获取安装阶段详细信息               |     以阶段在设备上安装软件相关事件记录在 Azure 数据中心。|
|     解锁或锁定 BitLocker 卷           |     记录事件，以指示 *basevolume* 和 *Hcsdata* 卷的 BitLocker 状态。|
|     净化磁盘                              |     记录了与无法删除的物理磁盘故障相关的事件，以及设备上的所有物理磁盘均已成功清除的事件。 |
|     启用或禁用本地用户               |     记录 Storsimpleadmin 身份和 PodSupportAdminUser 的本地用户帐户的启用或禁用相关事件。| 
|     密码重置                          |     将记录与为本地 Storsimpleadmin 身份用户成功或失败的密码重置相关的事件。 |


除了 INFINEON 审核日志之外，还会为 Data Box 收集保管链审核日志。 这些日志无法实时查看，只是在作业完成后，数据将从 Data Box 磁盘中删除。 这些日志包含 INFINEON 审核日志中包含的信息的子集。

有关监管审核日志链的详细信息，请参阅 [在数据擦除后获取保管链日志链](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure)。

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>访问审核日志

这些日志存储在 Azure 中，不能直接访问。 如果需要访问这些日志，请提交支持票证。 有关详细信息，请参阅[联系 Microsoft 支持部门](data-box-disk-contact-microsoft-support.md)。 

支持票证归档后，Microsoft 将下载并向你提供对这些日志的访问权限。


## <a name="next-steps"></a>后续步骤

- 了解如何[排查 Data Box 和 Data Box Heavy 上的问题](data-box-troubleshoot.md)。
