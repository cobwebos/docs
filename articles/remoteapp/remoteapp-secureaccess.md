---
title: "保护对 Azure RemoteApp 的访问安全，以及更多 | Microsoft Docs"
description: "学习如何通过在 Azure Active Directory 中使用条件访问保护对 Azure RemoteApp 的访问安全"
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: a19b0b09-ab26-4beb-80bb-33a46da39887
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 3cd588751346517f9359f760561839339b0f9edb


---
# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>保护对 Azure RemoteApp 的访问安全，以及更多
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

在本文中，我们将概要介绍管理员如何设置安全访问通道：从最终用户开始、经由 Azure RemoteApp、最后进入后端安全资源（例如 SQL 数据库或其他应用程序后端）。 目的是确保仅符合所需条件的授权用户可以访问远程应用程序，并确保仅可以从受控的 Azure RemoteApp 环境而不能从其他位置访问安全后端。

管理员需要查看的领域主要有以下 3 个：

![Azure RemoteApp 条件访问注意事项](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

阅读相关信息和以下这些问题的解答。

## <a name="who-can-access-the-collection"></a>谁可以访问该集合？
管理员选择可以访问集合中的远程应用程序的用户。 可使用 Azure Active Directory (Azure AD) 工作或学校帐户（先前称为“组织帐户”）或 Microsoft 帐户（例如 @outlook.com).）。大多数企业方案使用 Azure AD 帐户；在这些方案中，可使用稍后会讨论的条件访问功能，并且这些方案也是已加入域的集合的唯一选择。 本文的其余部分会假定你使用 Azure AD 帐户和 Azure RemoteApp。

**我们已完成的工作︰**

使用 Azure AD 帐户控制对 Azure RemoteApp 的访问告诉我们两件事情：

1. 我们始终知道谁可以访问我们发布的应用程序，以及谁也可以访问那些应用程序连接到的所有后端。
2. 我们控制基础 Azure AD，以便我们可以创建和删除用户帐户，设置密码策略，使用多重身份验证，等等。 

## <a name="how-is-the-collection-accessed-from-where"></a>该集合是如何访问的？ 从何处访问？
通常，管理员希望定义用于访问面向公共互联网的环境（比如 Azure RemoteApp）的策略。 例如，他们希望确保从公司网络外部访问该环境的用户必须使用多重身份验证 (MFA) 才能获得访问权限；或者，也许应该完全阻止这些用户。

Azure RemoteApp 管理员可以使用 Azure AD 高级版中提供的功能来设置其 Azure RemoteApp 环境的条件访问策略。 他们还可以使用丰富的报告和警报功能来监视访问环境的方式。

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>如何为 Azure RemoteApp 设置条件访问
我们将引导你完成一个示例方案 – 当用户处于企业网络外部时，Azure RemoteApp 管理员希望阻止对环境的访问。

> [!NOTE]
> 我们假定你已将 Azure AD 升级为高级层并且已创建至少一个 Azure RemoteApp 集合。
> 
> 

1. 在 Azure 门户上，单击“**Active Directory**”选项卡。 然后单击你想要配置的目录。
   
   请记住︰条件访问是目录的一个属性，而不是 Azure RemoteApp 的属性，因此，所有配置都是在目录级别完成的。 这也意味着你必须是目录管理员才能进行这些更改。
2. 单击“**应用程序**”，然后单击“**Microsoft Azure RemoteApp**”来设置条件访问。 请注意，可以单独为目录中的每个“服务型软件”应用程序设置条件访问。
   ![设置 Azure RemoteApp 的条件访问](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
3. 在“**配置**”选项卡上，将“**启用访问规则**”设置为“打开”。
   ![为 Azure RemoteApp 启用访问规则](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
4. 你现在可以配置各种规则并选择由谁将它们应用于︰
   
   1. 选择“**不工作时阻止访问**”，完全禁止用户从你指定的网络环境外部访问 Azure RemoteApp。
   2. 单击下面的选项，定义构成你的“受信任网络”的 IP 地址范围。 不在该 IP 地址范围内的所有 IP 地址都将被拒绝。
5. 可通过从你指定范围之外的某个 IP 地址启动 Azure RemoteApp 客户端来测试你的配置。 使用你的 Azure AD 凭据登录之后，你应该看到类似下面的一条消息︰

![已拒绝对 Azure RemoteApp 的访问](./media/remoteapp-secureaccess/ra-accessdenied.png)

### <a name="future-conditional-access-features"></a>未来的条件访问功能
Azure Active Directory 团队正致力于开发条件访问中的新功能。 除了基于网络位置的规则之外，管理员将能够创建新类型的规则。 新功能的公共预览版应该很快会推出。

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>如何监视对 Azure RemoteApp 的访问
Azure Active Directory Premium 报告功能是一个可以与条件访问一起使用的优良功能。 你可以使用这些报告来监视谁正在访问你的环境并检测任何可疑活动。

例如，你可以看到访问 Azure RemoteApp 的用户的名称，他们访问的次数以及访问时间。

1. 在 Azure 门户中，单击“**Active Directory**”，然后单击你的目录。
2. 转到“**报告**”选项卡。
3. 从报告的列表中，选择“**集成应用程序**”下的“**应用程序使用情况**”。
   
   你将看到 Azure RemoteApp 的一些聚合统计信息。 
   ![聚合的 Azure RemoteApp 访问统计信息](./media/remoteapp-secureaccess/ra-accessstats.png)
4. 单击应用程序以显示关于访问 Azure RemoteApp 的用户信息。
   ![Azure RemoteApp 的用户访问统计信息](./media/remoteapp-secureaccess/ra-userstats.png)

### <a name="summary"></a>摘要
使用 Azure Active Directory Premium 可以设置针对 Azure RemoteApp（以及通过 Azure AD 提供的其他服务型软件应用程序）的访问规则。 规则当前被限定为基于网络位置的策略，但在将来会扩展到企业管理的其他方面。

Azure AD Premium 还提供报告和监控功能，进一步扩展了管理员对其 Azure RemoteApp 环境的控制。

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>如何确保我的安全资源仅可以从我的 Azure RemoteApp 环境访问？
在本文的前面部分，我们主要介绍了保护对 Azure RemoteApp 环境的访问安全。 通过选择允许访问的用户并设置访问规则，可进一步控制他们可以如何使用该服务，我们已经实现了对 Azure RemoteApp 环境访问的安全保护。

Azure RemoteApp 部署的一个常见情形是远程应用程序需要与后端资源（例如 SQL 数据库）进行通信。 此资源要么托管在本地（例如在企业网络中）要么托管在云中（例如在 Azure IaaS 中）。 管理员通常需要确保后端资源仅可以由通过 Azure RemoteApp 部署的应用程序访问，而不能由直接在用户的 PC 上运行且通过公共 Internet 访问的应用程序访问。 Azure RemoteApp 通常被视为集中管理的安全环境，并因此被视为用户与后端资源进行交互应使用的唯一路径。

解决方案是将 Azure RemoteApp 环境和安全资源置于同一个 Azure 虚拟网络 (VNET) 中。 如果资源处于不同站点中，你可以建立一个站点到站点 VPN 连接，例如，创建一个跨 Azure 数据中心和客户本地环境的 VNet。

Azure RemoteApp 支持两种类型的集合部署，你可以在其中提供你自己的 VNET：

* 未加入域：应用程序将在 VNET 中具有其他资源的“视线”。 例如，这可以用于将应用程序连接到使用 SQL 身份验证的 SQL 数据库（应用程序直接针对数据库对用户进行身份验证）
* 已加入域︰Azure RemoteApp 所使用的虚拟机已加入 VNET 中的某个域控制器。 当应用程序为了获得对后端资源的访问，需要针对某个 Windows 域控制器进行身份验证时，这非常有用。
  ![Azure RemoteApp 中已加入域的集合](./media/remoteapp-secureaccess/ra-domainjoined.png)

### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>如何在 Azure 和我的本地环境之间创建安全连接
还有一些用于连接你的 Azure 和本地环境的配置选项。 此处很好地概述了这些选项。

使用 Azure RemoteApp 时，你需要首先配置你的 VNet，然后在集合创建过程中使用它。 

## <a name="the-complete-solution"></a>完整解决方案
下图显示了完整的解决方案，在这个方案中我们已经构建了一个从最终用户、经由 Azure RemoteApp (ARA)、进入后端资源的安全访问通道。
![保护 Azure RemoteApp 的安全](./media/remoteapp-secureaccess/ra-secureoverview.png)在阶段 1 中我们选择了用户，并创建了可控制如何访问 ARA 的访问规则。 在下面的示例中，我们仅允许在企业网络中工作的用户进行访问。 不符合条件的用户根本不能访问 ARA 环境。
在阶段 2 中，我们仅通过我们控制的 VNet VPN 配置公开了后端资源。 Azure RemoteApp 已被置于同一个 VNet 中。 最终结果是该资源仅可以通过 ARA 环境进行访问。




<!--HONumber=Dec16_HO2-->


