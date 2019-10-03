---
title: 排查对象不与 Azure Active Directory 同步的问题 | Microsoft Docs
description: 排查对象不与 Azure Active Directory 同步的问题。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60454945"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>排查对象不与 Azure Active Directory 同步的问题

如果对象未能按照预期同步到 Microsoft Azure Active Directory (Azure AD)，则可能存在多种原因。 如果收到来自 Azure AD 的有关错误的电子邮件，或者在 Azure AD Connect Health 中看到错误，请参阅[排查同步期间发生的错误](tshoot-connect-sync-errors.md)。 但是，如果要解决的问题所涉及的对象不在 Azure AD 中，则应使用本文作为参考。 本主题介绍如何在本地组件 Azure AD Connect 同步中查找错误。

>[!IMPORTANT]
>对于 1.1.749.0 或更高版本的 Azure AD Connect 部署，请使用向导中的[故障排除任务](tshoot-connect-objectsync.md)来排查对象同步问题。 

## <a name="synchronization-process"></a>同步过程

在调查同步问题之前，先了解一下 Azure AD Connect 同步过程：

  ![Azure AD Connect 同步过程示意图](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**术语**

* **CS：** 连接器空间，数据库中的一个表
* **MV：** Metaverse，数据库中的一个表

### <a name="synchronization-steps"></a>**同步步骤**
同步过程包括以下步骤：

1. **从 AD 导入：** 将 Active Directory 对象引入 Active Directory CS。

2. **从 Azure AD 导入：** 将 Azure AD 对象引入 Azure AD CS。

3. **同步：** 入站同步规则和出站同步规则按优先数字从低到高的顺序运行。 要查看同步规则，可以从桌面应用程序转到“同步规则编辑器”。 入站同步规则将数据从 CS 引入 MV。 出站同步规则将数据从 MV 移到 CS。

4. **导出到 AD：** 同步后，对象将从 Active Directory CS 导出到 Active Directory。

5. **导出到 Azure AD：** 同步后，对象将从 Azure AD CS 导出到 Azure AD。

## <a name="troubleshooting"></a>故障排除

若要查找错误，请按以下顺序查看几个不同位置的内容：

1. [操作日志](#operations)：查找导入和同步期间同步引擎识别的错误。
2. [连接器空间](#connector-space-object-properties)：查找缺少的对象和同步错误。
3. [Metaverse](#metaverse-object-properties)：查找与数据相关的问题。

在开始这些步骤之前，启动 [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md)。

## <a name="operations"></a>操作
应在 Synchronization Service Manager 中的“操作”选项卡处开始进行故障排除  。 此选项卡显示最新操作的结果。 

![Synchronization Service Manager 的屏幕截图，其中已选择“操作”选项卡](./media/tshoot-connect-object-not-syncing/operations.png)  

“操作”选项卡的上半部分按时间顺序显示所有运行  。 默认情况下，操作日志保留最后七天的相关信息，但可以使用[计划程序](how-to-connect-sync-feature-scheduler.md)来更改此设置。 查找所有未显示**成功**状态的运行。 可以单击标题来更改排序。

“状态”列包含最重要的信息，并显示最严重的运行问题。  下面是按调查优先级顺序显示的最常见状态的快速摘要（其中 * 表示多个可能的错误字符串）。

| 状态 | 注释 |
| --- | --- |
| stopped- * |运行无法完成。 例如，如果远程系统已关闭且无法访问，则可能发生此问题。 |
| stopped-error-limit |有 5,000 个以上的错误。 运行因错误数量过多而自动停止。 |
| completed-\*-errors |运行已完成，但发生应调查的错误（数量少于 5,000 个）。 |
| completed-\*-warnings |运行已完成，但某些数据并未处于预期的状态。 如果遇到错误，则此消息通常只是一种征兆。 在解决错误之前，请不要调查警告。 |
| 成功 |没有问题。 |

选择某一行时，“操作”选项卡的底部将更新以显示该运行的详细信息  。 在底部的最左侧，可能会显示标题为“步骤 #”的列表。  仅当林中有多个域，而且每个域都以一个步骤表示时，才会显示此列表。 可以在“分区”标题下方找到域名  。 在“同步统计信息”标题下，可以找到有关已处理更改次数的详细信息。  选择链接获取已更改对象的列表。 如果有对象发生错误，这些错误会显示在“同步错误”标题下。 

### <a name="errors-on-the-operations-tab"></a>“操作”选项卡上的错误
出现错误时，Synchronization Service Manager 将以链接形式显示出错的对象和错误本身，单击这些链接可获取更多信息。

![Synchronization Service Manager 中的错误屏幕截图](./media/tshoot-connect-object-not-syncing/errorsync.png)  
首先选择错误字符串。 （在上图中，错误字符串为 **sync-rule-error-function-triggered**。）随后会先看到对象概述。 若要查看实际的错误，请选择“堆栈跟踪”。  此跟踪提供错误的调试级别信息。

右键单击“调用堆栈信息”框，单击“全选”，然后选择“复制”。    然后复制堆栈，并在偏爱的编辑器（例如记事本）中查看此错误。

如果错误来自 **SyncRulesEngine**，则调用堆栈信息首先会列出对象上的所有属性。 向下滚动，直到看到 **InnerException =>** 标题为止。  

  ![Synchronization Service Manager 的屏幕截图，其中显示了 InnerException = > 标题下的错误信息](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
标题后面的行显示错误。 在上图中，错误来自 Fabrikam 创建的自定义同步规则。

如果错误中未提供足够的信息，请查看数据本身。 选择包含对象标识符的链接，继续对[连接器空间导入的对象](#cs-import)进行故障排除。

## <a name="connector-space-object-properties"></a>连接器空间对象属性
如果[**操作**](#operations)选项卡中未显示任何错误，请跟踪从 Active Directory 到 Metaverse 再到 Azure AD 的连接器空间对象。 在此路径中，应能找到问题所在。

### <a name="searching-for-an-object-in-the-cs"></a>搜索 CS 中的对象

在 Synchronization Service Manager 中，依次选择“连接器”、“Active Directory 连接器”、“搜索连接器空间”。  

在“范围”框中，选择“RDN”（如果想要搜索 CN 属性）或“DN 或定位点”（如果想要搜索 **distinguishedName** 属性）。    输入一个值并选择“搜索”  。 
 
![连接器空间搜索的屏幕截图](./media/tshoot-connect-object-not-syncing/cssearch.png)  

如果找不到要查找的对象，则可能已通过[基于域的筛选](how-to-connect-sync-configure-filtering.md#domain-based-filtering)或[基于 OU 的筛选](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)对其进行筛选。 若要验证是否已按预期方式配置了筛选，请参阅 [Azure AD Connect 同步：配置筛选](how-to-connect-sync-configure-filtering.md)。

可以通过选择 Azure AD 连接器执行另一种有用的搜索。 在“范围”框中选择“挂起的导入”，然后选中“添加”复选框。    此搜索提供 Azure AD 中不能与本地对象相关联的所有已同步对象。  

![连接器空间搜索中孤立对象的屏幕截图](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
这些对象是由其他同步引擎或具有不同筛选配置的同步引擎创建的。 不再管理这些孤立对象。 查看此列表并考虑使用 [Azure AD PowerShell](https://aka.ms/aadposh) cmdlet 删除这些对象。

### <a name="cs-import"></a>CS 导入
打开 CS 对象时，顶部会出现多个选项卡。 “导入”选项卡显示导入后暂存的数据。   

![“连接器空间对象属性”窗口的屏幕截图，其中已选择“导入”选项卡](./media/tshoot-connect-object-not-syncing/csobject.png)    

“旧值”列显示当前存储在 Connect 中的数据，而“新值”列显示从源系统收到但尚未应用的数据。   如果对象出现错误，则不会处理更改。

仅当对象出现问题时，“连接器空间对象属性”窗口中才会显示“同步错误”选项卡。   有关详细信息，请参阅如何[排查“操作”选项卡中显示的同步错误](#errors-on-the-operations-tab)。 

![“连接器空间对象属性”窗口中“同步错误”选项卡的屏幕截图](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS 沿袭
“连接器空间对象属性”窗口中的“沿袭”选项卡显示连接器空间对象与 Metaverse 对象关联的方式。   可以看到连接器上次从连接的系统导入更改的时间，以及应用哪些规则以便在 Metaverse 中填充数据。  

![“连接器空间对象属性”窗口中“沿袭”选项卡的屏幕截图](./media/tshoot-connect-object-not-syncing/cslineage.png)  

在上图中，“操作”列显示了一个操作为“预配”的入站同步规则。   这表示只要此连接器空间对象存在，就会保留 Metaverse 对象。 如果同步规则列表显示包含“预配”操作的出站同步规则，则删除 Metaverse 对象时，也会删除此对象。   

![“连接器空间对象属性”窗口中“沿袭”选项卡上的沿袭窗口屏幕截图](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

在上图中还可以看到，在“PasswordSync”列中，入站连接器空间可进行密码更改，因为有一个同步规则的值为 **True**。  此密码将通过出站规则发送到 Azure AD。

在“沿袭”选项卡中，可以选择 [**Metaverse 对象属性**](#mv-attributes)转到 Metaverse。 

### <a name="preview"></a>预览
“连接器空间对象属性”窗口的左下角提供了“预览”按钮。   选择此按钮会打开“预览”页，在其中可以同步单个对象。  如果你正在对某些自定义同步规则进行故障排除，并且想要在单个对象上查看更改的效果，则此页非常有用。 可以选择“完全同步”或“增量同步”   。还可以选择“生成预览”，这只会在内存中保留更改。  或者，可以选择“提交预览”，这会更新 Metaverse 并暂存对目标连接器空间的所有更改。   

![“预览”页的屏幕截图，其中已选择“开始预览”](./media/tshoot-connect-object-not-syncing/preview.png)  

在预览中，可以检查对象，并查看哪个规则应用到了特定的属性流。  

![“预览”页的屏幕截图，其中显示了“导入属性流”](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>日志
在“预览”按钮的旁边，选择“日志”按钮会打开“日志”页。    在此处可以查看密码同步状态和历史记录。 有关详细信息，请参阅[排查 Azure AD Connect 同步的密码哈希同步问题](tshoot-connect-password-hash-synchronization.md)。

## <a name="metaverse-object-properties"></a>Metaverse 对象属性
通常，最好从源 Active Directory 连接器空间开始搜索。 但也可以从 metaverse 开始搜索。

### <a name="searching-for-an-object-in-the-mv"></a>搜索 MV 中的对象
在 Synchronization Service Manager 中选择“Metaverse 搜索”，如下图所示。  创建一个查找用户的查询。 搜索公共属性，例如 **accountName** (**sAMAccountName**) 和 **userPrincipalName**。 有关详细信息，请参阅 [Sync Service Manager Metaverse 搜索](how-to-connect-sync-service-manager-ui-mvsearch.md)。

![Synchronization Service Manager 的屏幕截图，其中已选择“Metaverse 搜索”选项卡](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

在“搜索结果”  窗口中，单击对象。

如果未找到该对象，表示它尚未进入 Metaverse。 继续搜索 Active Directory [连接器空间](#connector-space-object-properties)中的对象。 如果在 Active Directory 连接器空间中找到该对象，则可能存在阻止对象进入 Metaverse 的同步错误，或者可能应用了同步规则范围筛选器。

### <a name="object-not-found-in-the-mv"></a>在 MV 中找不到对象
如果 Active Directory CS 中存在该对象，但 MV 中不存在该对象，则表示应用了范围筛选器。 若要查看范围筛选器，请转到桌面应用程序菜单并选择“同步规则编辑器”  。 通过调整下面的筛选器来筛选适用于该对象的规则。

  ![同步规则编辑器的屏幕快照，其中显示了入站同步规则搜索](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

查看上面列表中的每个规则，然后选中“作用域筛选器”  。 在下面的范围筛选器中，如果 **isCriticalSystemObject** 值为 null 或 FALSE 或为空，则表示在范围内。

  ![入站同步规则搜索中范围筛选器的屏幕截图](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

转到 [CS 导入](#cs-import)属性列表，查看阻止对象移到 MV 的筛选器。 “连接器空间”  属性列表仅显示非 null 和非空属性。 例如，如果 **isCriticalSystemObject** 未显示在列表中，则表示此属性的值为 null 或为空。

### <a name="object-not-found-in-the-azure-ad-cs"></a>在 Azure AD CS 中找不到对象
如果该对象不在 Azure AD 的连接器空间中，但在 MV 中，请查看相应连接器空间的出站规则的范围筛选器，并检查是否由于 [MV 属性](#mv-attributes)不符合条件而筛选掉了该对象。

要查看出站作用域筛选器，请通过调整下面的筛选器为对象选择适用的规则。 查看每个规则并查看相应的 [MV 属性](#mv-attributes)值。

  ![同步规则编辑器中出站同步规则搜索的屏幕快照](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV 属性
在“属性”选项卡上可以查看值，以及这些值是由哪些连接器提供的  。  

![“Metaverse 对象属性”窗口的屏幕截图，其中已选择“属性”选项卡](./media/tshoot-connect-object-not-syncing/mvobject.png)  

如果对象不同步，请提出有关 Metaverse 中的以下属性的问题：
- 属性 **cloudFiltered** 是否存在并设置为 **True**？ 如果是，则已根据[基于属性的筛选](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)中的步骤对其进行筛选。
- 属性“sourceAnchor”  是否存在？ 如果不存在，你是否拥有帐户资源林拓扑？ 如果对象被标识为链接的邮箱（属性 **msExchRecipientTypeDetails** 的值为 **2**），则由具有已启用的 Active Directory 帐户的林提供 **sourceAnchor**。 请确保已正确导入和同步主帐户。 主帐户必须在对象的[连接器](#mv-connectors)中列出。

### <a name="mv-connectors"></a>MV 连接器
“连接器”选项卡显示所有具有对象表示形式的连接器空间  。 
 
![“Metaverse 对象属性”窗口的屏幕截图，其中已选择“连接器”选项卡](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

应具有连接到以下项的连接器：

- 表示用户的每个 Active Directory 林。 此表示形式可以包括 **foreignSecurityPrincipals** 和 **Contact** 对象。
- Azure AD 中的连接器。

如果缺少连接到 Azure AD 的连接器，请查看 [MV 属性](#mv-attributes)，以验证有关预配到 Azure AD 的条件。

在“连接器”选项卡中也可以转到[连接器空间对象](#connector-space-object-properties)。  选择一行，并单击“属性”  。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure AD Connect 同步](how-to-connect-sync-whatis.md)。
- 详细了解[混合标识](whatis-hybrid-identity.md)。
