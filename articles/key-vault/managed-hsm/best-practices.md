---
title: 使用 Azure Key Vault 托管 HSM 的最佳实践
description: 本文档介绍了一些使用密钥保管库的最佳做法
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: ambapat
ms.openlocfilehash: 7a30a7ab6689b602bc9ad4f696a6fe54c80f2151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994277"
---
# <a name="best-practices-when-using-managed-hsm"></a>使用托管 HSM 时的最佳做法

## <a name="control-access-to-your-managed-hsm"></a>控制对托管 HSM 的访问

托管 HSM 是一项云服务，用于保护加密密钥。 由于这些密钥是敏感的并且业务关键，因此请确保仅允许授权的应用程序和用户安全访问托管的 Hsm。 [本文](access-control.md)概述了访问模型。 它介绍身份验证和授权，以及基于角色的访问控制。
- 为 HSM 管理员 (创建 [Azure Active Directory 安全组](../../active-directory/fundamentals/active-directory-manage-groups.md) ，而不是将管理员角色分配给) 个人。 这会阻止在删除单个帐户时出现 "管理锁定"。
- 锁定对管理组、订阅、资源组和托管 Hsm 的访问-使用 Azure RBAC 控制对管理组、订阅和资源组的访问权限
- 使用[托管 HSM 本地 RBAC](access-control.md#data-plane-and-managed-hsm-local-rbac)创建每个密钥角色分配
- 使用最低特权访问主体来分配角色

## <a name="choose-regions-that-support-availability-zones"></a>选择支持可用性区域的区域

- 若要确保高可用性和区域复原能力最佳，请选择支持 [可用性区域](../../availability-zones/az-overview.md) 的 Azure 区域。 这些区域在 Azure 门户中显示为 "推荐的区域"。

## <a name="backup"></a>备份

- 请确保定期备份 HSM。 可在 HSM 级别和针对特定密钥进行备份。 

## <a name="turn-on-logging"></a>启用日志

- 为 HSM[启用日志记录](logging.md)。 同时设置警报。

## <a name="turn-on-recovery-options"></a>启用恢复选项

- 默认情况下，[软删除](../general/soft-delete-overview.md)是打开的。
- 如果要防止在启用软删除后强制删除 HSM，请启用 "清除保护"。

## <a name="next-steps"></a>后续步骤

- 有关完整的 HSM 备份/还原的信息，请参阅 [完整备份/还原](backup-restore.md) 。
- 有关如何使用 Azure Monitor 配置日志记录的详细说明，请参阅[托管 HSM 日志记录](logging.md)
- 请参阅 [管理托管 HSM 密钥](key-management.md) 以进行密钥管理。
- 请参阅管理角色分配的 [托管 HSM 角色管理](role-management.md) 。
