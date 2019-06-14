---
title: 如何在 Azure AD Connect 中自定义同步规则 | Microsoft Docs
description: 本主题提供有关如何排查 Azure AD Connect 安装问题的步骤。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60351032"
---
# <a name="how-to-customize-a-synchronization-rule"></a>如何自定义同步规则

## <a name="recommended-steps"></a>**建议的步骤**

可以使用同步规则编辑器编辑或创建新同步规则。 需要作为高级用户才能对同步规则进行更改。 任何错误的更改都可能会导致从目标目录中删除对象。 请阅读[建议文档](#recommended-documents)以获得同步规则方面的专业知识。 若要修改同步规则，请完成以下步骤：

* 从桌面上的应用程序菜单启动同步编辑器，如下所示：

    ![同步规则编辑器菜单](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* 若要自定义默认同步规则，请通过单击同步规则编辑器上的“编辑”按钮（这会创建标准默认规则的副本并禁用它）来克隆现有规则。 使用小于 100 的优先级保存克隆的规则。  优先级可确定在发生属性流冲突时赢得冲突解决的规则（数值更小）。

    ![同步规则编辑器](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* 修改特定属性时，理想情况下只应在克隆的规则中保留修改属性。  然后启用默认规则，以便修改的属性来自克隆的规则，而其他属性从默认标准规则中选取。 

* 请注意，如果修改的属性的计算值在克隆的规则中为 NULL，在默认标准规则中不为 NULL，则中非 NULL 值会胜出并替换 NULL 值。 如果不希望 NULL 值替换为非 NULL 值，则在克隆的规则中分配 AuthoritativeNull。

* 若要修改“出站”  规则，请从同步规则编辑器更改筛选器。

## <a name="recommended-documents"></a>**建议的文档**
* [Azure AD Connect 同步：技术概念](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect 同步：了解体系结构](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect 同步：了解声明性预配](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect 同步：了解声明性预配表达式](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect 同步：了解默认配置](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect 同步：了解用户、组和联系人](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect 同步：影子属性](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>后续步骤
- [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- [什么是混合标识？](whatis-hybrid-identity.md)。
