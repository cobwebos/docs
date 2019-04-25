---
title: 对 Azure AD Connect 中未同步的属性排除故障 | Microsoft Docs
description: 本主题按步骤介绍了如何使用故障排除任务来排查属性同步问题。
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
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60455908"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>对 Azure AD Connect 中未同步的属性排除故障

## <a name="recommended-steps"></a>**建议的步骤**

在调查属性同步问题之前，先了解一下 Azure AD Connect 同步过程：

  ![Azure AD Connect 同步过程](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**术语**

* **CS：** 连接器空间，数据库中的一个表。
* **MV：** Metaverse，数据库中的一个表。
* **AD：** Active Directory
* **AAD：** Azure Active Directory

### <a name="synchronization-steps"></a>**同步步骤**

* 从 AD 导入：将 Active Directory 对象引入 AD CS。

* 从 AAD 导入：将 Azure Active Directory 对象引入 AAD CS。

* 同步：入站同步规则和出站同步规则按优先数字从低到高的顺序运行。 要查看同步规则，可以从桌面应用程序转到“同步规则编辑器”。 入站同步规则 将数据从 CS 引入 MV。 出站同步规则 将数据从 MV 移动到 CS。

* 导出到 AD：运行同步后，会将对象从 AD CS 导出到 Active Directory。

* 导出到 AAD：运行同步后，会将对象从 AAD CS 导出到 Azure Active Directory。

### <a name="step-by-step-investigation"></a>**逐步调查**

* 我们会从 Metaverse 开始搜索，并查看从源到目标的属性映射。

* 从桌面应用程序启动“Synchronization Service Manager”，如下所示：

  ![启动 Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* 在“Synchronization Service Manager”上，依次选择“Metaverse 搜索”、“按对象类型限定范围”和使用属性的对象，然后单击“搜索”按钮。

  ![Metaverse 搜索](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* 双击在 Metaverse 搜索中找到的对象以查看所有属性。 可以单击“连接器”选项卡以查看所有连接器空间中的对应对象。

  ![Metaverse 对象连接器](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* 双击“Active Directory 连接器”以查看连接器空间属性。 单击“预览”按钮，在后续对话框中单击“生成预览”按钮。

  ![连接器空间属性](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* 现在单击“导入属性流”，这会显示从 Active Directory 连接器空间到 Metaverse的属性流。 “同步规则”列显示影响该属性的同步规则。 “数据源”列显示来自连接器空间的属性。 “Metaverse 属性”列显示 Metaverse 中的属性。 可以此处查找未同步的属性。 如果在此处找不到属性，则此属性未映射，必须创建新的自定义同步规则以映射属性。

  ![连接器空间属性](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* 单击左窗格中的“导出属性流”以查看使用出站同步规则从 Metaverse 回到 Active Directory 连接器空间的属性流。

  ![连接器空间属性](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* 同样，可以查看 Azure Active Directory 连接器空间对象，并且可以生成预览以查看从 Metaverse到连接器空间及相反方向的属性流，这样便可以调查属性为何未同步。

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
