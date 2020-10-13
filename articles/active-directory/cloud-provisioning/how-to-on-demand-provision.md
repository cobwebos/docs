---
title: Azure AD Connect 云预配预配
description: 本文介绍按需预配功能。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72623d69f9fb0cbc68758a362bb977e5e02d2c00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91637062"
---
# <a name="azure-ad-connect-cloud-provisioning-on-demand-provisioning"></a>Azure AD Connect 云预配预配

Azure AD Connect 云预配引入了一项新功能，通过将这些更改应用于单个用户，可以测试配置更改。  你可以使用它来验证并验证对配置所做的更改是否已正确应用并正确地同步到 Azure AD。  

> [!IMPORTANT] 
> 使用按需预配时，范围筛选器将不会应用于所选用户。  这意味着，你可以在指定的 Ou 之外的用户上使用按需预配。


## <a name="using-on-demand-provisioning"></a>使用按需预配
若要使用新功能，请执行以下步骤。


1.  在 Azure 门户中，选择“Azure Active Directory”。
2.  选择“Azure AD Connect”****。
3.  选择 " **管理设置**"。

    ![管理预配](media/how-to-configure/manage1.png)
4. 在 " **配置**" 下，选择您的配置。
5. 在 " **验证** " 下单击 " **设置用户** " 按钮。 

 ![预配用户](media/how-to-on-demand-provision/on-demand2.png)

6. 在 "按需设置" 屏幕上。  输入用户的 **可分辨名称** ，然后单击 " **设置** " 按钮。  
 
 ![按需预配](media/how-to-on-demand-provision/on-demand3.png)
7. 完成后，应会看到 "成功" 屏幕和4个绿色复选框，指示已成功设置。  任何错误都将显示在左侧。

  ![成功](media/how-to-on-demand-provision/on-demand4.png)

现在，你可以查看用户并确定是否已应用你在配置中所做的更改。  本文档的其余部分将介绍已成功同步用户的详细信息中显示的各个部分。

## <a name="import-user-details"></a>导入用户详细信息
本部分提供有关从 Active Directory 导入的用户的信息。  这是用户在将其预配到 Azure AD 之前的样子。  单击 " **查看详细信息** " 链接以显示此信息。

![导入用户](media/how-to-on-demand-provision/on-demand5.png)

使用此信息，可以看到导入的各种属性及其值。  如果已创建自定义属性映射，则可以在此处看到值。
![导入用户详细信息](media/how-to-on-demand-provision/on-demand6.png)

## <a name="determine-if-user-is-in-scope-details"></a>确定用户是否处于范围详细信息
本部分提供有关已导入到 Azure AD 的用户是否在范围内的信息。  单击 " **查看详细信息** " 链接以显示此信息。

![用户范围](media/how-to-on-demand-provision/on-demand7.png)

使用此信息，可以查看有关用户范围的其他信息。

![用户范围详细信息](media/how-to-on-demand-provision/on-demand10a.png)

## <a name="match-user-between-source-and-target-system-details"></a>在源和目标系统的详细信息之间匹配用户
本部分提供有关用户是否已存在于 Azure AD 中的信息，是否应进行联接而不是设置新用户。  单击 " **查看详细信息** " 链接以显示此信息。
![查看详细信息](media/how-to-on-demand-provision/on-demand8.png)

使用此信息，可以查看是否找到了匹配项，或者是否要创建新用户。

![用户信息](media/how-to-on-demand-provision/on-demand11.png)

匹配详细信息将显示具有以下三个操作之一的消息。  它们是：
- "创建"-在 Azure AD 中创建用户
- 更新-用户根据配置中所做的更改进行更新
- 删除-用户已从 Azure AD 中删除。

根据执行的操作类型，消息将有所不同。

## <a name="perform-action-details"></a>执行操作详细信息
本部分提供有关在应用配置后已设置或导出到 Azure AD 的用户的信息。  这是用户在将其预配到 Azure AD 后的样子。  单击 " **查看详细信息** " 链接以显示此信息。
![执行操作详细信息](media/how-to-on-demand-provision/on-demand9.png)

使用此信息，可以在应用配置后查看属性的值。  它们看上去类似于导入的内容还是不同的内容？  配置是否成功？  

这将允许你跟踪属性转换，因为它通过云端进入到 Azure AD 租户。

![trace 特性](media/how-to-on-demand-provision/on-demand12.png)

## <a name="next-steps"></a>后续步骤 

- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
- [如何安装 Azure AD Connect 云预配](how-to-install.md)
 