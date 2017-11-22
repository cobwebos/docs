---
title: "使用 Azure 标识和访问控制保护个人数据 | Microsoft 文档"
description: "使用 Azure 标识和访问控制来帮助保护个人数据"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: e6de9526a1a72cfc81caca51207e000f8b3673cc
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory 和多重身份验证：使用标识和访问控制保护个人数据

本文提供使用 Azure Active Directory 和多重身份验证安全功能与服务保护个人数据时可以使用的信息和过程。

## <a name="scenario"></a>方案

总部位于美国的一家大型邮轮公司正在拓展其运营，以便在地中海、亚得里亚海和波罗的海以及英属岛屿提供路线。 为支持这些工作，该公司并购了意大利、德国、丹麦和英国的几家小型邮轮公司。 

该公司使用 Microsoft Azure 在云中存储企业数据。 这些数据包括个人身份信息，例如其全球客户群的姓名、地址、电话号码和信用卡信息。 另外，还包括传统的人力资源信息，例如，有关所有地点的公司员工的地址、电话号码、纳税识别号和其他信息。 该邮轮公司还维护一个奖励和忠诚计划会员的大型数据库，其中包含用于跟踪与当前和既往客户之间的关系的个人信息。

企业员工从公司的远程办公室访问网络，世界各地的旅行社有权访问某些公司资源。

## <a name="problem-statement"></a>问题陈述

该公司必须保护客户和员工个人数据的隐私，防止攻击者设法使用泄露的标识来获取访问权限。 他们还必须确保只有履行工作的合法用户才能访问个人数据。

## <a name="company-goal"></a>公司目标

公司的目标是确保对个人数据的访问受到严格控制。 有权访问个人数据的用户的标识受到强身份验证的保护，这一点至关重要。 必须强制实施 [最低特权] 策略(https://en.wikipedia.org/wiki/Principle_of_least_privilege)，使合法用户只拥有所需的访问权限级别，而没有更高的特权。

## <a name="solutions"></a>解决方案

Microsoft Azure 提供标识和访问管理工具来帮助公司控制谁有权访问包含个人数据的资源。

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) 可管理标识并控制对 Azure 以及其他本地资源、云资源、数据和应用程序的访问。 [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) 可帮助 Azure 管理员将有权访问某些信息（例如个人数据）的人员数量降至最低。 它可以让管理员发现、限制和监视特权标识及其对资源的访问，并向符合条件的用户分配暂时性的实时 (JIT) 管理权限。 它还提供有关谁拥有 AAD 管理权限的洞察信息。

涉及到使用 AAD PIM 的活动包括：

- 为目录启用 Privileged Identity Management

- 使用 Privileged Identity Management 管理仪表板查看重要信息的概览

- 通过添加或删除每个角色的永久性或符合条件的管理员，来管理特权标识（管理员）

- 配置角色激活设置

- 激活角色

- 查看角色活动

#### <a name="how-do-i-enable-aad-pim"></a>如何启用 AAD PIM？

若要开始为目录使用 PIM，请执行以下操作：

1. 以目录的全局管理员身份登录到 Azure 门户。

2. 如果组织有多个目录，请在 Azure 门户的右上角选择用户名。 选择会在其中使用 Azure AD Privileged Identity Management 的目录。

3. 选择“更多服务”，并使用“筛选器”文本框搜索“Azure AD Privileged Identity Management”。

4. 选中“固定到仪表板”，并单击“创建”。 Privileged Identity Management 应用程序打开。

设置 Azure AD Privileged Identity Management 后，每次打开应用程序时均会看到导航边栏选项卡。

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

有关开始使用 AAD PIM 的详细信息和说明，请参阅[开始使用 Azure AD Privileged Identity Management](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)。

### <a name="azure-role-based-access-control"></a>Azure 基于角色的访问控制

[Azure 基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) 可以基于用户的已分配角色授予访问权限，帮助 Azure 管理员管理对 Azure 资源的访问。 可在团队中对职责进行划分，仅授予执行作业所需的对用户、组和应用程序的适当访问权限。

可以使用 Azure 门户、Azure 命令行工具或 Azure 管理 API 将基于角色的访问权限授予用户。

有关 Azure RBAC 基础知识的详细信息，请参阅 [Azure 门户中基于角色的访问控制入门](https://docs.microsoft.com/active-directory/role-based-access-control-what-is)。

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>如何使用 PowerShell 管理 Azure RBAC？

可以使用 PowerShell cmdlet 管理 Azure RBAC，包括以下管理任务：

- 列出角色

- 查看谁具有访问权限

- 授予访问权限

- 删除访问权限

- 创建自定义角色

- 获取资源提供程序的操作

- 修改自定义角色

- 删除自定义角色

- 列出自定义角色

有关如何使用 PowerShell 管理 Azure RBAC 的说明，请参阅[使用 Azure PowerShell 管理基于角色的访问](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell)。

### <a name="azure-multi-factor-authentication"></a>Azure 多重身份验证

[Azure 多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) 是双重验证解决方案，可帮助保护对数据和应用程序的访问，同时可以满足用户对简单登录过程的需求。 它通过各种验证方法（包括电话、短信或移动应用验证）提供强大的身份验证机制。

若要在 Azure 云中部署 MFA，首先需要启用 MFA 并为用户启用双重验证。

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>如何让 Azure 使用 MFA？

如果用户拥有包括 Azure 多重身份验证的许可证，那么你只需按每个用户或组配置 Azure MFA 即可。 

![启用了 MFA 的用户](media/protect-personal-data-identity-access-controls/enable-mfa.png)

如果当前没有许可证，则需要完成为你的方案确定最适合部署类型的整个过程。 可以从查看标题为[选择 Azure 多重身份验证解决方案](../multi-factor-authentication/multi-factor-authentication-get-started.md)的文章开始。 如果确定需要创建一个多重身份验证服务器， 则可按以下步骤操作：

1. 在 Azure 门户中选择“Active Directory”（以管理员身份登录）。

2. 选择“MFA 服务器”

3. 指定一个超时值。 

    ![](media/protect-personal-data-identity-access-controls/mfa-server-settings.png)

4. 单击“保存”

在此窗口中，你还可以选择“下载 MFA 服务器”。 有关如何设置部署的规模并对其进行规划的更多详细信息，请参阅[开始使用 Azure 多重身份验证服务器](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)一文。

有关如何管理多重身份验证提供程序的详细说明，请参阅 [Azure 多重身份验证提供程序入门](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)。

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>如何为用户启用双重验证？

可以强制要求所有登录都进行双重验证，也可以创建条件访问策略，仅在特定的条件适用时才要求双重验证。

通过更改用户状态启用 Azure MFA 是要求进行双重验证的传统方法。 启用的所有用户在每次登录时都必须执行双重验证。 启用用户可以覆盖可能影响该用户的任何条件性访问策略。

使用条件访问策略启用 Azure MFA 是要求进行双重验证更为灵活的一种方法。 你可以创建应用于组和单个用户的条件性访问策略。 相对于低风险组，可以为高风险组指定更多的限制，或者只要求对高风险云应用进行双重验证，而跳过低风险组。 但是，条件访问是 Azure Active Directory 的一项付费功能。

若要通过更改用户状态来启用 MFA，请执行以下操作：

1. 以管理员身份登录到 Azure 门户。
2. 转到“Azure Active Directory”\>“用户和组”\>“所有用户”。
3. 选择“多重身份验证”。
4. 找到想要对其启用 Azure MFA 的用户。 可能需要在顶部切换视图。
5. 勾选用户名旁边的框。
6. 在右侧的快速步骤下，选择“启用”。

   ![](media/protect-personal-data-identity-access-controls/mfa-bulk.png)

7. 在打开的弹出窗口中确认你的选择。  启用 MFA 的用户在下次登录时必须注册。

若要使用条件访问策略启用 Azure MFA，请执行以下操作：

1. 以管理员身份登录到 Azure 门户。

2. 转到“Azure Active Directory”\>“条件访问”。

3. 选择“新策略”。

4. 在“分配”下，选择“用户和组”。 使用“包括”和“排除”选项卡，指定将由策略管理的用户和组。

5. 在“分配”下，选择“云应用”。 选择包含“所有云应用”。
6.  在“访问控制”下，选择“授予”。 选择“要求多重身份验证”。
7.  将“启用策略”设置为“启用”，然后选择“保存”。

有关如何配置 Azure MFA 设置来设置欺诈警报、创建免验证一次、使用自定义语音消息、配置缓存、指定受信任 IP、创建应用密码、允许用户信任的设备记住 MFA 和选择验证方法的信息，请参阅[配置 Azure 多重身份验证设置](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)。

## <a name="next-steps"></a>后续步骤

- [保护 Azure AD 中的特权访问](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [有关 Azure 多重身份验证的常见问题](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [基于角色的访问控制故障排除](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)
