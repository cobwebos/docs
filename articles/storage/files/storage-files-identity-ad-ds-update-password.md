---
title: 更新 AD DS 存储帐户密码
description: 了解如何更新代表存储帐户的 Active Directory 域服务帐户的密码。 这可以防止在密码过期时清除存储帐户，从而防止身份验证失败。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: d16f11a85c6b370b0187975cce965bf3e1b5ba3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85510248"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>更新中的存储帐户标识的密码 AD DS

如果你在强制密码过期时间的组织单位或域中注册了表示你的存储帐户的 Active Directory 域服务 (AD DS) 标识/帐户，则必须更改密码最长使用期限。 你的组织可能会运行自动清理脚本，这些脚本会在其密码过期后删除帐户。 因此，如果你在密码过期之前未更改密码，你的帐户将会被删除，从而导致你无法访问 Azure 文件共享。

若要触发密码轮换，可以 `Update-AzStorageAccountADObjectPassword` 从 [AzFilesHybrid 模块](https://github.com/Azure-Samples/azure-files-samples/releases)运行命令。 此命令必须在本地 AD DS 联接环境中运行，该环境使用具有存储帐户的所有者权限的混合用户和 AD DS 权限更改表示存储帐户的标识的密码。 命令执行类似于存储帐户密钥轮换的操作。 具体而言，它将获取存储帐户的第二个 Kerberos 密钥，并使用它来更新 AD DS 中注册帐户的密码。 然后，它将重新生成存储帐户的目标 Kerberos 密钥，并更新 AD DS 中注册帐户的密码。 必须在本地 AD DS 联接环境中运行此命令。

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
