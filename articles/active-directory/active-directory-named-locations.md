---
title: 在 Azure Active Directory 中配置命名位置 | Microsoft Docs
description: 了解如何配置命名位置。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 4c8aba8faf4ce91445209eb46abea17423f57a77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589325"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>在 Azure Active Directory 中配置命名位置

通过命名位置，可在组织中标记受信任的 IP 地址范围。 Azure Active Directory 在以下情况中使用命名位置：

- 检测[风险事件](active-directory-reporting-risk-events.md)，减少报告的误报数量。  

- [基于位置的条件性访问](active-directory-conditional-access-locations.md)。


本文介绍如何在环境中配置命名位置。


## <a name="entry-points"></a>入口点

要访问“Azure Active Directory”页“安全性”部分中的命名位置配置页，可单击：

![入口点](./media/active-directory-named-locations/34.png)

- **条件性访问：**

    - 在“管理”部分单击“命名位置”。
    
        ![命名位置命令](./media/active-directory-named-locations/06.png)

- **有风险的登录：**

    - 在顶部工具栏中，单击“添加已知的 IP 地址范围”。

       ![命名位置命令](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>配置示例

配置命名位置：

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。

2. 在左窗格中，单击“Azure Active Directory”。

    ![Azure Active Directory 链接位于左窗格中](./media/active-directory-named-locations/01.png)

3. 在“Azure Active Directory”页的“安全性”部分中，单击“条件性访问”。

    ![条件性访问命令](./media/active-directory-named-locations/05.png)


4. 在“Azure Active Directory”页的“管理”部分中，单击“命名位置”。

    ![命名位置命令](./media/active-directory-named-locations/06.png)


5. 在“命名位置”页上，单击“新建位置”。

    ![新建位置命令](./media/active-directory-named-locations/07.png)


6. 在“新建”页上执行以下操作：

    ![新建边栏选项卡](./media/active-directory-named-locations/61.png)

    a. 在“名称”框中，键入命名位置的名称。

    b. 在“IP 范围”框中，键入 IP 范围。 IP 范围需采用*无类别域际路由* (CIDR) 格式。  

    c. 单击“创建”。



## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [Azure Active Directory 中的条件访问](active-directory-conditional-access-azure-portal.md)。

- [Azure Active Directory 条件访问中的位置条件](active-directory-conditional-access-locations.md)

- [Azure Active Directory 风险事件](active-directory-reporting-risk-events.md)。

- [Azure Active Directory 门户中的“有风险的登录”报告](active-directory-reporting-security-risky-sign-ins.md)。  
