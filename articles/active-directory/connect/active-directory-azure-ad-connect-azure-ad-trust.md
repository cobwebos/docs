---
title: Azure AD Connect - 使用 Azure AD Connect 管理 AD FS 与 Azure AD 之间的信任关系 | Microsoft Docs
description: 有关使用 Azure AD Connect 处理 Azure AD 信任的操作详细信息。
keywords: AD FS, ADFS, AD FS 管理, AAD Connect, Connect, Azure AD, 信任, AAD, 声明, 声明规则, 颁发, 转换, 规则, 备份, 还原
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: mtillman
ms.component: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.openlocfilehash: a037414f16c3986370557f389328d36788e2c292
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494819"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>使用 Azure AD Connect 管理 AD FS 与 Azure AD 之间的信任关系

## <a name="overview"></a>概述

Azure AD Connect 可以管理本地 Active Directory 联合身份验证服务 (AD FS) 与 Azure AD 之间的联合。 本文提供以下方面的概述：

* Azure AD Connect 针对信任配置的各种设置
* Azure AD Connect 设置的颁发转换规则（声明规则）
* 如何在备份和还原升级与配置更新之间的声明规则。 

## <a name="settings-controlled-by-azure-ad-connect"></a>Azure AD Connect 控制的设置

Azure AD Connect **仅**管理与 Azure AD 信任相关的设置。 Azure AD Connect 不会修改 AD FS 中有关其他信赖方信任的任何设置。 下表指出了 Azure AD Connect 控制的设置。

| 设置 | Description |
| :--- | :--- |
| 令牌签名证书 | Azure AD Connect 可用于重置和重新创建与 Azure AD 之间的信任关系。 Azure AD Connect 针对 AD FS 的令牌签名证书执行一次性的立即滚动更新，并更新 Azure AD 域联合设置。|
| 令牌签名算法 | Microsoft 建议使用 SHA-256 作为令牌签名算法。 Azure AD Connect 可以检测令牌签名算法是否设置为安全性不如 SHA-256 的值。 在下一个可能的配置操作中，它会将设置更新为 SHA-256。 |
| Azure AD 信任标识符 | Azure AD Connect 为 Azure AD 信任设置正确的标识符值。 AD FS 使用该标识符值唯一标识 Azure AD 信任。 |
| Azure AD 终结点 | Azure AD Connect 确保为 Azure AD 信任配置的终结点始终符合最新建议的复原能力和性能值。 |
| 颁发转换规则 | 为确保联合设置中的 Azure AD 性能和功能达到最佳，需要用到许多的声明规则。 Azure AD Connect 确保始终使用适当的建议声明规则集来配置 Azure AD 信任。 |
| Alternate-id | 如果同步配置为使用 alternate-id，Azure AD Connect 会将 AD FS 配置为使用 alternate-id 来执行身份验证。 |
| 自动元数据更新 | 为 Azure AD 信任配置自动元数据更新。 AD FS 定期检查 Azure AD 信任的元数据，如果这些元数据在 Azure AD 端发生更改，则 AD FS 会将其保持最新状态。 |
| Windows 集成身份验证 (IWA) | 在执行混合 Azure AD 加入操作期间，将启用 IWA 来进行设备注册，以方便下层设备完成混合 Azure AD 加入 |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Azure AD Connect 配置的执行流和联合设置

在执行配置流期间，Azure AD Connect 不会更新 Azure AD 信任的所有设置。 修改的设置取决于正在执行的任务或执行流。 下表列出了不同执行流中受影响的设置。

| 执行流 | 受影响的设置 |
| :--- | :--- |
| 第一轮安装（快速） | 无 |
| 第一轮安装（新 AD FS 场） | 创建新的 AD FS 场，并从头开始创建与 Azure AD 之间的信任关系。 |
| 第一轮安装（现有 AD FS 场，现有 Azure AD 信任） | Azure AD 信任标识符、颁发转换规则、Azure AD 终结点、Alternate-id（如果需要）、自动元数据更新 |
| 重置 Azure AD 信任 | 令牌签名证书、令牌签名算法、Azure AD 信任标识符、颁发转换规则、Azure AD 终结点、Alternate-id（如果需要）、自动元数据更新 |
| 添加联合服务器 | 无 |
| 添加 WAP 服务器 | 无 |
| 设备选项 | 颁发转换规则、用于设备注册的 IWA |
| 添加联合域 | 首次添加域时（即，设置从单域联合更改为多域联合身份验证），Azure AD Connect 会从头开始重新创建信任。 如果为多个域配置了 Azure AD 信任，则只修改颁发转换规则 |
| 更新 SSL | 无 |

在执行所有操作期间（修改了任何设置），Azure AD Connect 会在 **%ProgramData%\AADConnect\ADFS** 位置创建当前信任设置的备份

![Azure AD Connect 页，其中显示了有关现有 Azure AD 信任备份的消息](media/active-directory-azure-ad-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> 在低于 1.1.873.0 的版本中，备份仅包括颁发转换规则，并且这些规则备份在向导跟踪日志文件中。

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Azure AD Connect 设置的颁发转换规则

Azure AD Connect 确保始终使用适当的建议声明规则集来配置 Azure AD 信任。 Microsoft 建议使用 Azure AD Connect 来管理 Azure AD 信任。 本部分列出颁发转换规则集及其说明。

| 规则名称 | Description |
| --- | --- |
| 颁发 UPN | 此规则从 userprincipalname 的同步设置中配置的属性查询 userprincipalname 的值。|
| 查询自定义 ImmutableId 声明的 objectguid 和 msdsconsistencyguid | 此规则在 objectguid 和 msdsconsistencyguid 值（如果存在）的管道中添加一个临时值 |
| 检查是否存在 msdsconsistencyguid | 基于 msdsconsistencyguid 的值是否存在，设置一个临时标志用于指示要将哪个值用作 ImmutableId |
| 如果 msdsconsistencyguid 存在，则颁发 msdsconsistencyguid 作为不可变 ID | 如果 msdsconsistencyguid 值存在，则颁发 msdsconsistencyguid 作为 ImmutableId |
| 如果 msdsConsistencyGuid 规则不存在，则颁发 objectGuidRule | 如果 msdsconsistencyguid 的值不存在，将颁发 objectguid 的值作为 ImmutableId |
| 颁发 nameidentifier | 此规则颁发 nameidentifier 声明的值。|
| 颁发已加入域的计算机的 accounttype | 如果进行身份验证的实体是已加入域的设备，则此规则颁发帐户类型，作为表示已加入的域的设备的 DJ |
| 如果 AccountType 不是计算机帐户，则颁发值为 USER 的 AccountType | 如果进行身份验证的实体是用户，则此规则颁发 User 帐户类型 |
| 如果 AccountType 不是计算机帐户，则颁发 issuerid | 如果身份验证实体不是设备，则此规则颁发 issuerId 值。 该值通过 Azure AD Connect 配置的正则表达式创建。 该正则表达式是在考虑到使用 Azure AD Connect 联合的所有域后创建的。 |
| 颁发用于 DJ 计算机身份验证的 issuerid | 如果身份验证实体是设备，则此规则颁发 issuerId 值 |
| 颁发已加入域的计算机的 onpremobjectguid | 如果进行身份验证的实体是已加入的域的设备，则此规则颁发该设备的本地 objectguid |
| 直通主要 SID | 此规则颁发身份验证实体的主要 SID |
| 直通声明 - insideCorporateNetwork | 此规则颁发一个声明，帮助 Azure AD 确定身份验证是来自企业网络的内部还是外部 |
| 直通声明 – Psso |   |
| 颁发密码过期声明 | 此规则颁发三个声明，它们对应于密码过期时间、进行身份验证的实体的密码过期天数，以及用于更改密码的路由 URL。|
| 直通声明 – authnmethodsreferences | 根据此规则颁发的声明中的值指示对实体执行的身份验证类型 |
| 直通声明 - multifactorauthenticationinstant | 此声明的值指定用户上次执行多重身份验证的 UTC 时间。 |
| 直通声明 - AlternateLoginID | 如果使用备用登录 ID 执行了身份验证，则此规则颁发 AlternateLoginID 声明。 |

> [!NOTE]
> 如果在 Azure AD Connect 配置期间使用了非默认选项，“颁发 UPN”和 ImmutableId 的声明规则将会不同

## <a name="restore-issuance-transform-rules"></a>还原颁发转换规则

每当对 Azure AD 信任设置进行更新时，Azure AD Connect 1.1.873.0 或更高版本都会创建 Azure AD 信任设置的备份。 Azure AD 信任设置将备份到 **%ProgramData%\AADConnect\ADFS**。 文件名采用以下格式：AadTrust-&lt;日期&gt;-&lt;时间&gt;.txt，例如 AadTrust-20180710-150216.txt

![Azure AD 信任示例备份的快照](media/active-directory-azure-ad-connect-azure-ad-trust/backup.png)

可以使用下面建议的步骤还原颁发转换规则

1. 在服务器管理器中打开 AD FS 管理 UI
2. 转到“AD FS”&gt;“信赖方信任”&gt;“Microsoft Office 365 标识平台”&gt;“编辑声明颁发策略”，打开 Azure AD 信任属性
3. 单击“添加规则”
4. 在声明规则模板中，选择“使用自定义规则发送声明”并单击“下一步”
5. 从备份文件复制声明规则的名称，并将其粘贴到“声明规则名称”字段中
6. 将备份文件中的声明规则复制到“自定义规则”的文本字段中，并单击“完成”

> [!NOTE]
> 确保其他规则不与 Azure AD Connect 配置的规则相冲突。

## <a name="next-steps"></a>后续步骤
* [使用 Azure AD Connect 管理和自定义 Active Directory 联合身份验证服务](active-directory-aadconnect-federation-management.md)