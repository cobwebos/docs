---
title: Azure EA 门户管理
description: 本文介绍管理员可在 Azure EA 门户中完成的常见任务。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 236a8d6d280e75102df84fec5ab3df51f7ba355b
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105486"
---
# <a name="azure-ea-portal-administration"></a>Azure EA 门户管理

本文介绍管理员可在 Azure EA 门户 (https://ea.azure.com) 中完成的常见任务。 Azure EA 门户是一个在线管理门户，可帮助客户管理其 Azure EA 服务的成本。 有关 Azure EA 门户的简介信息，请参阅 [Azure EA 门户入门](billing-ea-portal-get-started.md)一文。

## <a name="add-a-new-enterprise-administrator"></a>添加新的企业管理员

企业管理员拥有管理 Azure EA 注册的大部分特权。 设置 EA 协议时，会创建初始的 Azure EA 管理员。 但是，你随时可以添加或删除新管理员。 新管理员只能由现有的管理员添加。 有关添加更多企业管理员的详细信息，请参阅[创建另一个企业管理员](billing-ea-portal-get-started.md#create-another-enterprise-admin)。有关计费配置文件角色和任务的详细信息，请参阅[计费配置文件角色和任务](billing-understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="update-user-state-from-pending-to-active"></a>将用户状态从“挂起”更新为“活动”

首次将新的帐户所有者 (AO) 添加到 Azure EA 注册时，其状态将显示为“挂起”。  当新帐户所有者收到激活欢迎电子邮件时，他们可以登录以激活其帐户。 激活帐户后，其帐户状态将从“挂起”更新为“活动”。   系统可能会提示新用户输入其名字和姓氏来创建商务帐户。 如果出现此提示，他们必须添加所需的信息以继续，然后其帐户将会激活。

## <a name="add-a-department-admin"></a>添加部门管理员

Azure EA 管理员创建部门后，Azure 企业管理员可以添加部门管理员并将每个管理员关联到某个部门。 部门管理员可以创建新帐户。 需要添加新帐户才能创建 Azure EA 订阅。

有关添加部门的详细信息，请参阅“创建 Azure EA 部门”。

## <a name="enterprise-user-roles"></a>企业用户角色

Azure EA 门户可帮助你管理 Azure EA 的成本和使用情况。 Azure EA 门户中有三个主要角色：

- EA 管理员
- 部门管理员
- 帐户所有者

每个角色具有不同的访问权限和权力级别。

有关用户角色的详细信息，请参阅“企业用户角色”。

## <a name="add-an-azure-ea-account"></a>添加 Azure EA 帐户

Azure EA 帐户是 Azure EA 门户中的一个组织单位，用于管理订阅和报告。 若要访问和使用 Azure 服务，需要创建一个帐户，或使用已创建的帐户。

有关 Azure 帐户的详细信息，请参阅“添加帐户”。

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>将企业帐户转移到新注册

将企业帐户转移到新注册时，请记住以下几点：

- 仅转移请求中指定的帐户。 如果选择所有帐户，会转移所有这些帐户。
- 源注册的状态将保留为活动或已扩展。 在该注册过期之前，可以继续使用它。

### <a name="effective-transfer-date"></a>转移生效日期

转移生效日期可以是要转移到的注册的开始日期当日或之后的某个日期。 要转移到的注册称为目标注册。  转移帐户后，帐户中在转移生效日期之前的所有使用情况信息将保留在要从中转移的注册中。 要从中转移的注册称为源注册。   源注册的使用费根据货币承诺或超额计收。 在转移生效日期之后发生的使用费将转移到新注册并相应地收费。

可将帐户转移的日期调后，只要不早于目标注册的开始日期即可。 或者，最晚可以调到源注册的生效开始日期。

### <a name="monetary-commitment"></a>货币承诺

货币承诺不可在注册之间转移。 货币承诺余额在契约上关联到订购服务的注册。 在帐户或注册转移过程中，不会转移货币承诺。

### <a name="services-affected"></a>受影响的服务

帐户转移期间不会发生停机。 如果已提供全部所需的信息，则转移可以在请求的同一日完成。

### <a name="prerequisites"></a>先决条件

请求帐户转移时，请提供以下信息：


- 要转移的帐户的名称和所有者 ID
- 对于源注册，请提供要转移的注册编号和帐户
- 对于目标注册，请提供要转移到的注册编号
- 帐户转移生效日期可以是目标注册开始日期当日或之后的某个日期

转移帐户之前要注意的其他要点：

- 目标和源注册需要经过 EA 管理员的批准
  - 在某些情况下，Microsoft 可能会要求源注册的 EA 管理员做出附加批准
- 如果帐户转移不符合要求，请考虑进行注册转移。
- 帐户转移过程会转移所有服务、订阅、帐户、部门和整个注册结构，包括所有 EA 部门管理员。
- 帐户转移会将源注册状态设置为“已转移”。  转移的帐户仅可用于历史使用情况报告。
- 无法将角色或订阅添加到处于已转移状态的注册。 该状态可防止注册进一步产生使用费。
- 源协议中任何剩余的货币承诺余额将会丢失，包括将来的条款。


## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>将企业注册转移到新注册

请求将整个企业注册转移到某个注册时，将发生以下操作：

- 将转移所有 Azure 服务、订阅、帐户、部门和整个注册结构，包括所有 EA 部门管理员。
- 注册状态将设置为“已转移”。  转移的注册仅可用于历史使用情况报告。
- 无法将角色或订阅添加到处于已转移状态的注册。 已转移状态可防止注册进一步产生使用费。
- 协议中任何剩余的货币承诺余额将会丢失，包括将来的条款。

### <a name="effective-transfer-date"></a>转移生效日期

转移生效日期可以是要转移到目标注册的注册开始日期当日或之后的某个日期。

源注册的使用费根据货币承诺或超额计收。 在转移生效日期之后发生的使用费将转移到新注册并相应地收费。

### <a name="effective-transfer-date-in-the-past"></a>过去的转移生效日期

可将帐户转移的日期调后，只要不早于目标注册的开始日期即可。 或者，最晚可以调到源注册的生效开始日期。

### <a name="monetary-commitment"></a>货币承诺

货币承诺不可在注册之间转移。 货币承诺余额在契约上关联到订购服务的注册。 在帐户或注册转移过程中，不会转移货币承诺。

### <a name="services-affected"></a>受影响的服务

帐户转移期间不会发生停机。 如果已提供所有必备信息，则转移可以在请求的同一日完成。

### <a name="prerequisites"></a>先决条件

请求注册转移时，请提供以下信息：

- 对于源注册，请提供要转移的注册编号和帐户
- 对于目标注册，请提供要转移到的注册编号
- 注册转移生效日期可以是目标注册开始日期当日或之后的某个日期 所选日期不能影响任何已开具的超额发票的使用。

转移注册之前要注意的其他要点：

- 目标和源注册需要经过 EA 管理员的批准
  - 在某些情况下，Microsoft 可能会要求源注册的 EA 管理员做出附加批准
- 如果注册转移不符合要求，请考虑进行帐户转移。
- 仅转移指定的帐户。 可以请求转移所有帐户。
- 源注册的状态将保留为活动/已扩展。 在该注册过期之前，可以继续使用它。

## <a name="change-account-owner"></a>更改帐户所有者

Azure EA 门户可将一个帐户所有者的订阅转移到另一个帐户所有者。 有关详细信息，请参阅[更改帐户所有者](billing-ea-portal-get-started.md#change-account-owner)。

## <a name="subscription-transfer-effects"></a>订阅转移的影响

将 Azure 订阅转移到同一 Azure Active Directory 租户中的某个帐户后，以前拥有[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 权限，可管理资源的所有用户、组和服务主体将保留其访问权限。

若要查看哪些用户对订阅拥有 RBAC 访问权限：

1. 在 Azure 门户中，打开 **订阅**。
2. 选择要查看的订阅，然后选择“访问控制(IAM)”。 
3. 选择“角色分配”  。 角色分配页将列出对订阅拥有 RBAC 访问权限的所有用户。

如果将订阅转移到了不同 Azure AD 租户中的某个帐户，则以前拥有 [RBAC](../role-based-access-control/overview.md) 权限，可管理资源的所有用户、组和服务主体将失去其访问权限。  尽管失去了 RBAC 访问权限，但他们可以通过安全机制来访问订阅，这些机制包括：

- 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../cloud-services/cloud-services-certs-create.md)。
- 存储空间等服务的访问密钥。 有关详细信息，请参阅 [Azure 存储帐户概述](../storage/common/storage-account-overview.md)。
- Azure 虚拟机等服务的远程访问凭据。

如果接收方需要限制对其 Azure 资源的访问，他们应考虑更新与服务关联的任何机密。 可通过以下步骤更新大多数资源：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单上，选择“所有资源”  。
3. 选择资源。
4. 在资源页上，单击“设置”以查看和更新现有的机密。 



## <a name="close-an-azure-enterprise-enrollment"></a>关闭 Azure 企业注册

若要关闭 Azure EA 注册，可以：

- 在 Azure 门户上取消与你的 Azure EA 关联的所有订阅。
- 联系客户软件顾问或合作伙伴，或者请求他们关闭 Azure 企业协议。

## <a name="update-notification-settings"></a>更新通知设置

企业管理员会自动注册接收与其注册相关的使用情况通知。 每个企业管理员可以更改每项通知的发送间隔，或者完全禁用通知。

通知联系人显示在 Azure EA 门户中的“通知联系人”部分。  管理通知联系人可确保由组织中的适当人员接收 Azure EA 通知。

若要查看当前通知设置：

1. 在 Azure EA 门户中，导航到“管理” > “通知联系人”。  
2. 电子邮件地址 – 与接收通知的企业管理员的 Microsoft 帐户、工作或学校帐户关联的电子邮件地址。
3. 未开票余额通知频率 – 设置向每个企业管理员发送通知的频率。

若要添加联系人：

1. 单击“+添加联系人”。 
2. 输入电子邮件地址，然后确认。
3. 单击“ **保存**”。

新的通知联系人将显示在“通知联系人”部分。  若要更改通知频率，请选择通知联系人，然后单击所选行右侧的铅笔符号。 将频率设置为“每日”、“每周”、“每月”或“无”。    

可以取消“即将达到计费周期结束日期”和“即将达到禁用和取消预配日期”生命周期通知。   禁用生命周期通知会取消有关计费周期和协议结束日期的通知。

## <a name="manage-partner-notifications"></a>管理合作伙伴通知

合作伙伴管理员可以管理其注册使用情况通知的接收频率。 他们每周会自动收到未开票余额的通知。 他们可将每项通知的频率更改为每月、每周、每日，或完全禁用通知。

如果用户未收到某项通知，请使用以下步骤验证该用户的通知设置是否正确。

1. 以合作伙伴管理员的身份登录到 Azure EA 门户。
2. 单击“管理”，然后单击“合作伙伴”选项卡。  
3. 在“管理员”部分下查看管理员列表。 
4. 若要编辑通知首选项，请将鼠标悬停在相应的管理员上，然后单击铅笔符号。
5. 根据需要更新通知频率和生命周期通知。
6. 根据需要添加联系人，然后单击“添加”。 
7. 单击“ **保存**”。

![显示“添加联系人”的示例 ](./media/billing-ea-portal-administration/create-ea-manage-partner-notification.png)

## <a name="azure-sponsorship-offer"></a>Azure 赞助套餐
Azure 赞助套餐是一个受限的 Microsoft Azure 赞助帐户。 它是 Microsoft 通过电子邮件邀请提供给有限的客户使用的套餐。 如果你符合 Microsoft Azure 赞助套餐的条件，你的帐户 ID 会收到一封邀请电子邮件。
有关详细信息，请参阅：

- 赞助套餐概述 - https://azure.microsoft.com/en-us/offers/ms-azr-0143p/
- 赞助余额门户 - https://www.microsoftazuresponsorships.com/balance  
- 赞助外部使用常见问题解答 - https://azuresponsorships-staging.azurewebsites.net/faq
- 有关激活赞助的支持请求 - http://aka.ms/azrsponsorship

## <a name="next-steps"></a>后续步骤
- 了解[虚拟机预留项](billing-ea-portal-vm-reservations.md)如何帮助节省资金。
- 在排查 Azure EA 门户问题时如需帮助，请参阅[排查 Azure EA 门户访问问题](billing-ea-portal-troubleshoot.md)。
