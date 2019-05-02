---
title: 为 Microsoft 客户协议的 Azure 设置计费帐户 |Microsoft Docs
description: 了解如何为 Microsoft 客户协议设置计费帐户。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: 284480ab8b9973c02f365dbf3ebfd3a103ef4b42
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919152"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>为 Microsoft 客户协议设置计费帐户

如果你的企业协议注册已过期或即将过期，可以注册 Microsoft 客户协议续订你的注册。 续订包括以下步骤：

1. 接受新的 Microsoft 客户协议。 使用 Microsoft 字段代表联系以了解详细信息并接受新的协议。
2. 设置为新的 Microsoft 客户协议创建新的计费帐户。

本文介绍了安装后，现有计费更改，并将指导你完成设置新的计费帐户。

若要设置计费帐户，必须转换 Azure 订阅的计费从你的企业协议注册到新帐户。 安装程序不会影响你的订阅中运行的 Azure 服务。 但是，它更改为你的订阅管理计费的方式。

- 而不是[EA 门户](https://ea.azure.com)，将在管理 Azure 服务和计费[Azure 门户](https://portal.azure.com)。
- 你将获得每月、 数字发票用于你的费用。 你可以查看和分析中的 Azure 成本管理 + 计费页面发票。
- 而不是部门和在企业协议注册中的帐户，将使用的计费结构和作用域从新的帐户来管理和组织您的付费。

在启动安装程序之前，我们建议你以下操作：

- **了解新的计费帐户**
  - 新帐户简化了为你的组织计费。 [获取新的计费帐户的快速概述](billing-mca-overview.md)
- **验证你的访问以完成设置**
  - 只有具有特定的管理权限的用户可以完成设置。 检查您是否具有[以完成安装所需访问权限](#access-required-to-complete-the-setup)。
- **了解对你计费的层次结构的更改**
  - 您新帐务帐户组织方式与你的企业协议注册。 [了解计费层次结构中的新帐户将变为](#understand-changes-to-your-billing-hierarchy)。
- **了解对计费管理员的访问权限的更改**
  - 从你的企业协议注册管理员有权访问新帐户中的计费作用域。[了解到他们的访问权限更改](#understand-changes-to-your-billing-administrators-access)。
- **替换为新帐户的 view 企业协议功能**
  - 查看替换为新帐户中的功能的企业协议注册功能。
- **视图最常见的问题的解答**
  - 视图[的其他信息](#additional-information)若要了解有关安装程序的详细信息。

## <a name="access-required-to-complete-the-setup"></a>若要完成安装所需访问权限

若要完成的安装，需要以下访问权限：

- Microsoft 客户协议进行签名时所创建的计费配置文件的所有者。 若要了解有关计费的配置文件的详细信息，请参阅[了解计费资料](billing-mca-overview.md#understand-billing-profiles)。

- 在续订时注册的企业管理员。

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>如果您不上注册的企业管理员

你可以请求在注册的企业管理员，以完成您的计费帐户设置。

1. 登录到 Azure 门户中使用签名 Microsoft 客户协议时发送给你的电子邮件中的链接。

2. 如果你的组织中的其他人登录协议或您没有电子邮件，使用以下链接登录。 替换**enrollmentNumber**使用已续订的企业协议注册号。

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. 选择你想要将请求发送的企业管理员。

   ![屏幕截图，显示邀请企业管理员](./media/billing-mca-setup-account/ea-mca-invite-admins.png)

4. 选择**发送请求**。

   管理员将收到一封电子邮件的说明完成安装。

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>如果您不计费的配置文件的所有者

用户在你的组织，Microsoft 客户协议签名者添加为上计费的配置文件的所有者。 请求用户，以便您可以完成安装程序将你添加为所有者。  <!-- Todo Are there any next steps -->

## <a name="understand-changes-to-your-billing-hierarchy"></a>了解对计费层级结构的更改

新的计费帐户简化了为你的组织同时提供增强的计费和成本管理功能计费。 下图说明如何计费的组织结构的新的计费帐户。

![Ea-mca 开机自检-转换-层次结构的图像](./media/billing-mca-setup-account/mca-post-transition-hierarchy.png)

1. 计费帐户用于为 Microsoft 客户协议管理计费。 若要了解有关计费帐户的详细信息，请参阅[了解计费帐户](billing-mca-overview.md#understand-billing-account)。
2. 计费的配置文件用于为你的组织，类似于你的企业协议注册管理计费。 企业管理员成为计费的配置文件的所有者。 若要了解有关计费的配置文件的详细信息，请参阅[了解计费资料](billing-mca-overview.md#understand-billing-profiles)。
3. 发票部分用于组织您根据需要，类似于在企业协议注册中的部门的成本。 部门变为发票部分，部门管理员成为相应发票部分的所有者。 若要了解有关发票部分的详细信息，请参阅[了解发票部分](billing-mca-overview.md#understand-invoice-sections)。
4. 在你的企业协议中创建的帐户不支持在新的计费帐户中。 为其院系的相应发票部分属于帐户的订阅。 帐户所有者可以创建和管理其发票的部分的订阅。

## <a name="understand-changes-to-your-billing-administrators-access"></a>了解对计费管理员的访问权限的更改

具体取决于其访问权限，在企业协议合约的帐务管理员有权访问新帐户上的计费作用域。 下表说明在安装过程中访问的更改：

| 现有的角色 | Post 转换角色 |
| --- | --- |
| **企业管理员 (读取仅 = No)** | **计费配置文件所有者** </br> 管理计费的配置文件中的所有内容 </br> - **发票部分所有者对所有发票部分** </br> 管理发票部分中的所有内容 |
| **企业管理员 (读取仅 = Yes)** | **计费配置文件读取器** </br> 的只读视图的所有内容在计费帐户</br>**发票上所有发票部分的部分读取器**</br> 的只读视图的所有内容上的发票部分|
| **部门管理员 (读取仅 = No)** |**发票上创建为其各自的院系的发票部分的部分所有者** </br>管理发票部分中的所有内容|
| **部门管理员 (读取仅 = Yes)**|**发票部分读取器上创建为其各自的院系的发票部分**</br> 只读的所有内容的视图上的发票部分|
| **帐户所有者** | **的创建为其各自的院系的发票部分上 azure 订阅创建者** </br>  创建 Azure 订阅，其发票部分|

登录 Microsoft 客户协议时，如果为新的计费帐户选择 Azure Active Directory 租户。 如果租户不存在为你的组织中，创建一个新租户。 租户代表你的组织在 Azure Active Directory 中。 你组织中的全局租户管理员使用租户来管理应用程序和你的组织中的数据的访问权限。

新的帐户仅支持来自签名 Microsoft 客户协议时选定的租户的用户。 如果具有企业协议上的管理权限的用户是租户的成员，他们将在安装过程中获得新的计费帐户的访问权限。 如果它们不是租户的成员，它们将无法访问新的计费帐户，除非您邀请他们。

当您邀请的用户时，它们作为来宾用户添加到租户，并获取计费帐户的访问权限。 若要邀请的用户，则租户的来宾访问权限必须打开。 有关详细信息，请参阅[控制在 Azure Active Directory 中的来宾访问](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory)。 如果来宾访问处于关闭状态，请联系你的租户以将其打开的全局管理员。 <!-- Todo - How can they find their global administrator -->

## <a name="view-features-replaced-by-the-new-billing-account"></a>查看功能替换为新的计费帐户

以下企业协议的功能替换为计费帐户中的新增功能 Microsoft 客户协议。

### <a name="enterprise-agreement-accounts"></a>企业协议帐户

在你的企业协议注册期间创建的帐户不支持在新的计费帐户中。 该帐户的订阅属于创建为其各自的院系的发票部分。 帐户所有者成为 Azure 订阅创建者，以及可以创建和管理其发票的部分的订阅。

### <a name="notification-contacts"></a>通知联系人

通知联系人发送有关 Azure 企业协议的电子邮件通信。 它们不支持在新的计费帐户中。 有关 Azure 额度和发票的电子邮件发送到在您的计费帐户有权访问计费的配置文件的用户。

### <a name="spending-quotas"></a>支出配额

已设置为在企业协议注册中的部门的支出配额替换为新的计费帐户中的预算。 为每个设置在注册中的部门的支出配额创建预算。 预算的详细信息，请参阅[创建和管理 Azure 预算](../cost-management/manage-budgets.md)。

### <a name="cost-centers"></a>成本的中心

在企业协议注册中的 Azure 订阅设置的成本的中心一同转移的新的计费帐户。 但是，不支持部门和企业协议客户的成本中心。

## <a name="additional-information"></a>其他信息

以下部分提供有关如何设置计费帐户的其他信息。

### <a name="no-service-downtime"></a>服务不会中断

订阅内的 Azure 服务保持正常运行，不会中断。 我们只转换 Azure 订阅的计费关系。 这不会对现有的资源、资源组或管理组产生影响。

### <a name="user-access-to-azure-resources"></a>对 Azure 资源的用户访问权限

使用 Azure RBAC （基于角色的访问控制） 设置到 Azure 资源的访问权限在转换期间不受影响。

### <a name="azure-reservations"></a>Azure 预留

在企业协议注册中的任何 Azure 预订移到新的计费帐户。 在转换期间，对订阅应用的预留折扣不会有任何更改。

### <a name="azure-marketplace-products"></a>Azure Marketplace 产品

企业协议许可登记表中的任何 Azure Marketplace 产品订阅一起移动。 在转换期间不会有对 Marketplace 产品的服务的访问权限的任何更改。

### <a name="support-plan"></a>支持计划

支持权益无法转移为转换的一部分。 购买新的支持计划，以利用新的计费帐户中的 Azure 订阅。

### <a name="past-charges-and-balance"></a>过去的费用和平衡

通过 Azure 门户在企业协议注册中，可以查看在转换之前的费用和信用额度余额。 <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>何时完成安装程序？

企业协议注册到期前，请完成您的计费帐户的设置。 如果你注册到期后，你的 Azure 订阅中的服务将仍然继续在运行而不发生中断。 但是，您将零售费率收取的服务。

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>安装后，企业协议注册到的更改

转换可以手动将移动到新的计费帐户之后为企业协议注册创建的 azure 订阅。 有关详细信息，请参阅[获取计费的其他用户的 Azure 订阅所有权](billing-mca-request-billing-ownership.md)。 若要移动的过渡后，购买 Azure 预订[联系 Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 您还可以提供用户计费帐户的访问权限后转换。 有关详细信息，请参阅[管理计费在 Azure 门户中的角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)

### <a name="revert-the-transition"></a>恢复转换

无法还原转换。 一旦你的 Azure 订阅的计费过渡到新的计费帐户，你不能还原它为企业协议注册。

### <a name="closing-your-browser-during-setup"></a>在安装过程中关闭你的浏览器

在您单击之前**开始转换**，可以关闭浏览器。 可以返回到使用了电子邮件中的链接的安装程序并启动转换。 如果您关闭浏览器中，启动转换后，转换将继续在运行。 回到转换状态页来监视您转换的最新状态。 转换完成时，你将收到一封电子邮件。

## <a name="complete-the-setup-in-the-azure-portal"></a>完成在 Azure 门户中设置

若要完成的安装，需要对新的计费帐户和企业协议注册的访问。 有关详细信息，请参阅[需完成的计费帐户设置访问权限](#access-required-to-complete-the-setup)。

1. 登录到 Azure 门户中使用签名 Microsoft 客户协议时发送给你的电子邮件中的链接。

2. 如果你的组织中的其他人登录协议或您没有电子邮件，使用以下链接登录。 替换**enrollmentNumber**使用已续订的企业协议注册号。

   `https://portal.azure.com/#blade/Microsoft_Azure_Billing/EATransitionToMCA/enrollmentId/enrollmentNumber`

3. 选择**开始转换**中安装的最后一步。 一旦选择开始转换：

    ![显示安装向导的屏幕截图](./media/billing-mca-setup-account/ea-mca-set-up-wizard.png)

    - 新的计费帐户中创建对应于你的企业协议层次结构的计费层次结构。 有关详细信息，请参阅[了解对你计费的层次结构的更改](#understand-changes-to-your-billing-hierarchy)。
    - 从你的企业协议注册的管理员都被赋予访问新的计费帐户，以便他们继续管理为你的组织的帐单。
    - 你的 Azure 订阅的计费过渡到新帐户。 **不会有任何影响针对 Azure 服务在此转换。它们将继续运行而无需中断**。
    - 如果具有 Azure 保留项，它们都移到新的计费帐户具有相同的折扣和期限。 预订折扣将继续应用转换的过程。

4. 可以在监视的转换状态**转换状态**页。

   ![显示转换状态的屏幕截图](./media/billing-mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-the-billing-account-is-set-up-properly"></a>验证计费帐户已正确设置

 验证以下内容，以确保正确设置了新的计费帐户：

### <a name="azure-subscriptions"></a>Azure 订阅

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“成本管理 + 计费”中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. 选择计费的配置文件。 根据你拥有的访问权限，可能需要选择一个计费帐户。 从计费帐户，选择**计费配置文件**，然后计费的配置文件。

4. 选择**Azure 订阅**从左侧和右侧。

   ![显示订阅的列表的屏幕截图](./media/billing-mca-setup-account/billing-mca-subscriptions-post-transition.png)

从你的企业协议注册转换为新的计费帐户的 azure 订阅的 Azure 订阅页上显示。 如果您认为不缺少任何订阅，转换在 Azure 门户中手动订阅的计费。 有关详细信息，请参阅[获取计费的其他用户的 Azure 订阅的所有权](billing-mca-request-billing-ownership.md)

### <a name="azure-reservations"></a>Azure 预留

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“成本管理 + 计费”中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. 选择发票部分。 根据你拥有的访问权限，可能需要选择一个计费帐户或计费配置文件。  从计费帐户或帐单的配置文件中，选择**发票部分**，然后发票部分。

    ![显示的发票部分 post 转换列表的屏幕截图](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. 选择**所有产品**从左侧和右侧。

5. 搜索上**保留**。

    ![显示的订阅文章转换列表的屏幕截图](./media/billing-mca-setup-account/billing-mca-azure-reservations-post-transition.png)

从你的企业协议注册移动到新的计费帐户的 azure 保留所有的产品页上显示。 重复所有发票节来验证所有 Azure 预订都移动从你的企业协议注册的步骤。 如果你认为任何 Azure 预订缺少[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)将预订移动到新的计费帐户。

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>企业管理员的计费的配置文件的访问权限

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“成本管理 + 计费”中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-mca-setup-account/billing-search-cost-management-billing.png)

3. 选择创建你的注册的计费配置文件。 根据你拥有的访问权限，可能需要选择一个计费帐户。  从计费帐户，选择**计费配置文件**，然后计费的配置文件。

4. 选择**访问控制 (IAM)** 从左侧和右侧。

   ![显示企业管理员 post 转换的访问权限的屏幕截图](./media/billing-mca-setup-account/billing-mca-ea-admins-access-post-transition.png)

企业管理员列为计费配置文件读取器为计费时列出具有只读权限的管理员在企业的配置文件所有者。 如果您认为不缺少任何企业管理员的访问权限，您可以授予他们访问在 Azure 门户中。 有关详细信息，请参阅[管理在 Azure 门户中的计费角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>企业管理员、 部门管理员和帐户所有者的发票部分的访问权限

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在“成本管理 + 计费”中进行搜索。

   ![显示了 Azure 门户搜索的屏幕截图](./media/billing-mca-setup-account/billing-search-cost-management-billing.png).

3. 选择发票部分。 发票部分具有企业协议合约中其各自部门与相同的名称。 具体取决于您的访问权限，可能需要选择计费的配置文件或计费帐户。 从计费的配置文件或计费帐户中选择**发票部分**，然后选择发票部分。

   ![显示的发票部分 post 转换列表的屏幕截图](./media/billing-mca-setup-account/billing-mca-invoice-sections-post-transition.png)

4. 选择**访问控制 (IAM)** 从左侧和右侧。

    ![显示部门和管理员访问 post 转换的帐户的访问权限的屏幕截图](./media/billing-mca-setup-account/billing-mca-department-account-admins-access-post-transition.png)

企业管理员和部门管理员列为发票部分所有者或发票部分读取器时在部门中的帐户所有者列出为 Azure 订阅创建者。 重复所有发票部分来检查在企业协议注册中的所有部门的访问权限的步骤。 没有任何部门的一部分的帐户所有者会在名为发票部分获取的权限**默认发票部分**。 如果您认为不缺少任何管理员的访问权限，您可以授予他们访问在 Azure 门户中。 有关详细信息，请参阅[管理在 Azure 门户中的计费角色](billing-understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [开始使用新的计费帐户](billing-mca-overview.md)

- [Microsoft 客户协议完成您的计费帐户中的企业协议任务](billing-mca-enterprise-operations.md)

- [管理对您的计费帐户的访问](billing-understand-mca-roles.md)
