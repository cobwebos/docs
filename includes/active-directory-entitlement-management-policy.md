---
title: include 文件
description: include 文件
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/16/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: ef8155cda6c31a63204af80ca091a13bb16a687d
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866446"
---
### <a name="policy-for-users-in-your-directory"></a>策略:在目录中的用户

如果你想要你可以请求访问此包的目录中的用户和组策略，请执行以下步骤。

1. 在中**可以请求访问权限的用户**部分中，选择**你的目录中的用户**。

1. 在中**选择用户和组**部分中，单击**添加用户和组**。

1. 在选择用户和组窗格中，选择用户和你想要添加的组。

    ![访问包的策略-选择用户和组](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. 单击**选择**若要添加的用户和组。

1. 跳到[策略：请求](#policy-request)部分。

### <a name="policy-for-users-not-in-your-directory"></a>策略:不在目录中的用户

如果您希望在策略适用于不可以请求此访问包目录中的用户，请执行以下步骤。 目录必须配置为允许**组织关系协作限制**设置。

1. 在中**可以请求访问权限的用户**部分中，选择**不在目录中的用户**。

1. 在中**选择外部 Azure AD 目录**部分中，单击**添加目录**。

1. 输入域名和搜索外部 Azure AD 目录。

1. 验证正确的目录中提供的目录名称和初始域。

    > [!NOTE]
    > 目录中的所有用户将都能够请求此访问权限的包。 这包括与该目录，而不仅仅是在搜索中使用的域关联的所有子域的用户。

    ![访问包的策略选择目录](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. 单击**添加**若要添加的目录。

1. 重复此步骤以添加任何更多的目录。

1. 已添加所有目录后你想要包括在策略中，单击**选择**。

1. 跳到[策略：请求](#policy-request)部分。

### <a name="policy-none-administrator-direct-assignments-only"></a>策略:无 （仅管理员直接分配）

如果你要绕过访问请求，并允许管理员将直接分配给访问包的特定用户的策略，请执行以下步骤。 用户不需要请求访问包。 您仍可以设置到期时间设置，但没有请求的设置。

1. 在中**可以请求访问权限的用户**部分中，选择**None (直接分配管理员**。

    创建访问包后，可以直接向访问包分配特定的内部和外部用户。 如果指定的外部用户，将在目录中创建来宾用户帐户。

1. 跳到[策略：过期](#policy-expiration)部分。

### <a name="policy-request"></a>策略:请求

在请求部分中，可以指定批准设置当用户请求访问包。

1. 若要为所选用户的请求需要审批，设置**需要批准**切换到**是**。 若要自动批准的请求，请将开关设置为**否**。

1. 如果在需要审批，**选择审批者**部分中，单击**添加审批者**。

1. 在选择审批者窗格中，选择一个或多个用户和/或组为审批者。

    所选审批者之一需要批准的请求。 所有审批者的批准不是必需的。 该审批决策基于任何审批者首先检查请求。

    ![访问包的策略选择审批者](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. 单击**选择**添加审批者。

1. 单击**显示高级请求设置**以显示其他设置。

    ![访问包的策略选择目录](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. 若要要求用户提供理由以请求访问包，请设置**需要理由**到**是**。

1. 如果需要审批者以提供理由以批准的访问包的请求，请设置**需要审批者理由**到**是**。

1. 在中**审批请求超时时间 （天）** 框中，指定审批者必须查看请求的时间量。 没有审批者查看它在此天数，如果请求的截止日期，所以用户必须提交另一个请求访问包。

### <a name="policy-expiration"></a>策略:过期时间

在过期部分中，指定何时过期的用户分配到访问包。

1. 中**到期**部分中，设置**访问包过期**到**日期**，**天数**，或**从不**.

    有关**日期**，选择将来的到期日期。

    有关**天数**，指定 0 到 3660 天之间的数字。

    在某一天，一定数量的天数后它们已得到批准，过期根据你的选择，对访问包的用户的分配或从不。

1. 单击**显示高级过期设置**以显示其他设置。

1. 若要允许用户将其分配，请设置**允许用户将扩展访问**到**是**。

    如果在策略中允许扩展，则用户将收到一封电子邮件 14 天和 1 天之前其访问包分配设置为过期也提示他们延期分配。

    ![访问包的策略的到期时间设置](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>策略:启用策略

1. 如果你想要对立即可用的策略中的用户的访问包，请单击**是**来启用的策略。

    你可以始终启用它将来后，已创建访问包。

    ![访问包的支持策略的策略设置](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. 单击**下一步**或**创建**。
