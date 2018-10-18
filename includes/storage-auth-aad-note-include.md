---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/06/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3a3ee0cda3643ac73581f868e47905841d9f1563
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095585"
---
> [!IMPORTANT]
> - 适用于 blob 和队列的 Azure AD 身份验证预览版仅可用于非生产用途。 生产服务级别协议 (SLA) 当前不可用。 如果你的方案尚不支持 Azure AD 身份验证，请继续使用应用程序中的共享密钥授权或 SAS 令牌。
>
> - 预览期间，RBAC 角色分配可能需要最多五分钟的时间进行传播。
>
> - 当调用 blob 和队列操作时，必须使用 HTTPS 来向 Azure AD 证明身份。
>
> - 在预览版本中，Azure 门户不使用 Azure AD 凭据来读取和写入 blob 和队列数据。 相反，门户将继续依赖于帐户访问密钥。 若要在门户中查看或更新 blob 或队列数据，必须为用户分配一个包含了 [Microsoft.Storage/storageAccounts/listkeys/action](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role) 的 RBAC 角色，该角色授权用户调用[存储帐户 - 列出密钥](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/listkeys)。 适用于 blob 和队列的“参与者”和“读者”角色当前未将 **listkeys** 操作包括为预览版本的一部分，因此，请不要通过 Azure 门户提供数据访问。 在预览版本中，若要对 blob 和队列数据进行基于标识的访问，请使用 PowerShell 或 Azure CLI。
