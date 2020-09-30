---
title: 托管 HSM 本地 RBAC 内置角色 - Azure Key Vault | Microsoft Docs
description: 有关可分配给用户、服务主体、组和托管标识的托管 HSM 内置角色的概述性介绍
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 6b39c1d6fe2e84055e7c94dde076514d04a24f36
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992142"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>托管 HSM 本地 RBAC 内置角色

托管 HSM 本地 RBAC 具有多个内置角色，可将这些角色分配给用户、服务主体、组和托管标识。 若要允许主体执行某一操作，需要为其分配一个角色，该角色授予其执行该操作的权限。 通过这些角色和操作，你只能管理数据平面操作的权限。 若要管理托管 HSM 资源的控制平面权限（如创建新的托管 HSM 或更新、移动、删除现有的 HSM），必须使用 [Azure 基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md)。

## <a name="built-in-roles"></a>内置角色

|角色名称|说明|ID|
|---|---|---|
|托管 HSM 管理员| 授予对所有数据操作的完全访问权限。|a290e904-7015-4bba-90c8-60543313cdb4|
|托管 HSM 加密管理人员| 授予对所有密钥管理和密钥加密操作的完全访问权限|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|托管 HSM 加密用户|授予为加密操作创建和使用密钥的权限。 不能永久删除密钥。|21dbd100-6940-42c2-9190-5d6cb909625b|
|托管 HSM 策略管理员| 授予创建和删除角色分配的权限|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|托管 HSM 加密审核者|授予读取（但不使用）密钥的读取权限|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|托管 HSM 加密服务加密| 授予使用密钥进行服务加密的权限。 |33413926-3206-4cdd-b39a-83574fe37a17|
|托管 HSM 备份| 授予执行单个密钥或完整 HSM 备份的权限。 |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>允许执行的运算
> [!NOTE]  
> - “X”表示允许某个角色执行数据操作。 空单元格表示角色没有执行该数据操作的权限。
> - 所有数据操作名称均带有“Microsoft.KeyVault/managedHsm”前缀，为简洁起见，下表省略了该前缀。
> - 所有角色名称均带有“托管 HSM”前缀，为简洁起见，下表省略了该前缀。

|数据操作 | 管理员 | 加密管理人员 | 加密用户 | “策略管理员” | 加密服务加密 | 备份 | 加密审核者|
|---|---|---|---|---|---|---|---|
|**安全域管理**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**密钥管理**|
|/keys/read/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/create|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/delete|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/read/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/recover/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/delete|<center>X</center>|<center>X</center>|||||<center>X</center>|
|/keys/backup/action|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>|
|/keys/restore/action|<center>X</center>|<center>X</center>|||||
|/keys/export/action|<center>X</center>|<center>X</center>|||||
|/keys/import/action|<center>X</center>|<center>X</center>|||||
|**密钥加密操作**|
|/keys/encrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/decrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/wrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/unwrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/sign/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/verify/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|**角色管理**|
|/roleAssignments/delete/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/read/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/write/action|<center>X</center>|||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|||<center>X</center>|||
|**备份/还原管理**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 的概述。
- 请参阅有关[托管 HSM 角色管理](role-management.md)的教程
