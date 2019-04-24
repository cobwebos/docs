---
title: Azure AD Connect 同步：更改默认配置 | Microsoft Docs
description: 提供有关更改 Azure AD Connect 同步默认配置的最佳实践。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 940a35d89996b1eb9600fe4214863d2b5304750e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60242122"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect 同步：更改默认配置的最佳做法
本主题旨在说明支持和不支持的 Azure AD Connect 同步更改。

通过 Azure AD Connect 创建的配置无需更改即可适用于同步本地 Active Directory 与 Azure AD 的大多数环境。 但是，在某些情况下，必须对配置应用某些更改，以满足特殊需求或要求。

## <a name="changes-to-the-service-account"></a>服务帐户的更改
Azure AD Connect 同步在安装向导创建的服务帐户下运行。 此服务帐户保存了同步使用的数据库加密密钥。它是使用 127 个字符长的密码创建的，密码设置为永不过期。

* **不支持**更改或重置服务帐户的密码。 这样做会破坏加密密钥，服务将无法访问数据库且无法启动。

## <a name="changes-to-the-scheduler"></a>计划程序的更改
从内部版本 1.1（2016 年 2 月）开始，可以将[计划程序](how-to-connect-sync-feature-scheduler.md)配置为使用非默认的同步周期（默认周期为 30 分钟）。

## <a name="changes-to-synchronization-rules"></a>同步规则的更改
安装向导提供的配置应该适用于最常见的方案。 如果需要对配置进行更改，必须遵循这些规则，以便仍可保留支持的配置。

> [!WARNING]
> 如果更改默认同步规则，则下次更新 Azure AD Connect 时将覆盖这些更改，从而导致意外且可能无用的同步结果。

* 如果默认的直接属性流不适用于组织，可以[更改属性流](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes)。
* 如果希望[属性不流动](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute)并要删除 Azure AD 中的任何现有属性值，需要为此方案创建规则。
* [禁用不需要的同步规则](#disable-an-unwanted-sync-rule)而不是删除它。 升级期间将重新创建已删除的规则。
* 若要[更改现成的规则](#change-an-out-of-box-rule)，应复制原始规则并禁用现成的规则。 同步规则编辑器将显示提示并提供帮助。
* 使用同步规则编辑器导出自定义同步规则。 编辑器提供一个 PowerShell 脚本，可以在灾难恢复方案中使用它轻松重新创建同步规则。

> [!WARNING]
> 现成的同步规则具有指纹。 如果更改这些规则，指纹不再匹配。 今后尝试应用 Azure AD Connect 的新版本时可能会遇到问题。 只能根据本文所述的方式进行更改。

### <a name="disable-an-unwanted-sync-rule"></a>禁用不需要的同步规则
不要删除现成的同步规则。 下一次升级期间会重新创建该规则。

在某些情况下，安装向导生成的配置不适用于拓扑。 例如，如果使用帐户资源林拓扑，但已在具有 Exchange 架构的帐户林中扩展该架构，则系统针对帐户林和资源林创建适用于 Exchange 的规则。 在此情况下，需要禁用适用于 Exchange 的同步规则。

![已禁用同步规则](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

在上图中，安装向导已在帐户林中找到旧的 Exchange 2003 架构。 此架构扩展是在 Fabrikam 环境中引入资源林之前添加的。 若要确保不同步任何来自旧 Exchange 实现的属性，应该按所述方式禁用同步规则。

### <a name="change-an-out-of-box-rule"></a>更改现成的规则
仅当需要更改联接规则时，才需更改现成规则。 如果需要更改属性流，则应创建具有比现成规则优先级更高的同步规则。 实际上，只有 **In from AD - User Join** 这一条规则需要克隆。 可使用优先级更高的规则替代所有其他规则。

如果需要对现成的规则进行更改，应该复制该现成的规则，并禁用原始规则。 然后对克隆的规则进行更改。 同步规则编辑器会帮助完成这些步骤。 打开现成的规则时，会显示此对话框：  
![对现成规则的警告](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

选择“是”创建规则的副本。 随后会打开克隆的规则。  
![克隆的规则](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

在这个克隆的规则中，对范围、联接和转换进行任何必要的更改。

## <a name="next-steps"></a>后续步骤
**概述主题**

* [Azure AD Connect 同步：了解和自定义同步](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)
