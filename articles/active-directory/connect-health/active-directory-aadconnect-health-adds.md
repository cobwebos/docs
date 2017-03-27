---
title: "在 AD DS 中使用 Azure AD Connect Health | Microsoft 文档"
description: "本页与 Azure AD Connect Health 相关，介绍如何监视 AD DS。"
services: active-directory
documentationcenter: 
author: arluca
manager: samueld
editor: curtand
ms.assetid: 19e3cf15-f150-46a3-a10c-2990702cd700
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9d7640577eedcc9221f6346b650aed85f1d31a65
ms.openlocfilehash: 26ebdbc6f568dd3d9bbcaa3250aae70d80af2d35


---
# <a name="using-azure-ad-connect-health-with-ad-ds"></a>在 AD DS 中使用 Azure AD Connect Health
以下文档专门介绍如何使用 Azure AD Connect Health 来监视 Active Directory 域服务。 支持的 AD DS 版本为：Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 和 Windows Server 2016。

有关使用 Azure AD Connect Health 监视 AD FS 的详细信息，请参阅 [在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)。 此外，有关使用 Azure AD Connect Health 监视 Azure AD Connect（同步）的信息，请参阅 [使用用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)。

![用于 AD DS 的 Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-entry.png)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>用于 AD DS 的 Azure AD Connect Health 的警报
用于 AD DS 的 Azure AD Connect Health 中的“警报”部分提供一个与域控制器相关的活动和已解决警报列表。 如果选择某个活动或已解决警报，将打开一个新的边栏选项卡，其中包含一些附加信息、解决方法步骤和支持文档链接。 每种警报类型可以有一个或多个实例，它们对应于受该特定警报影响的每一个域控制器。 在警报边栏选项卡底部附近，可以双击某个受影响的域控制器打开另一个边栏选项卡，其中包含有关该警报实例的更多详细信息。

在此边栏选项卡中，可为警报启用电子邮件通知，以及更改视图中的时间范围。 扩大时间范围可以查看以前已解决的警报。

![Azure AD Connect 同步错误](./media/active-directory-aadconnect-health/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>域控制器仪表板
此仪表板提供环境拓扑视图，以及每个受监视域控制器的关键操作指标和运行状况。 所显示的指标可帮助快速识别可能需要进一步调查的任何域控制器。 默认情况下，仅显示列的子集。 但是，可以通过双击 columns 命令找到整个可用列集。 通过选择最感兴趣的列，让此仪表板成为一个可轻松查看 AD DS 环境运行状况的地方。

![域控制器](./media/active-directory-aadconnect-health/aadconnect-health-adds-domainsandsites-dashboard.png)

域控制器可按其各自的域或站点分组，这有助于了解环境拓扑。 最后，如果双击边栏选项卡标题，仪表板将最大化，以便充分利用可用的屏幕空间。 显示多个列时，放大的视图非常方便。

## <a name="replication-status-dashboard"></a>复制状态仪表板
此仪表板可用于查看受监视的域控制器的复制状态和复制拓扑。 仪表板中将列出最新复制尝试操作的状态，以及针对任何已找到的错误的帮助文档。 可以双击某个出错的域控制器打开一个新的边栏选项卡，其中显示了如下所述的信息：有关错误的详细信息、建议的解决步骤，以及疑难解答文档的链接。

![复制状态](./media/active-directory-aadconnect-health/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>监视
此功能提供不同性能计数器的图形趋势，这些趋势信息是从每个受监视的域控制器持续收集的。 可以根据林中其他所有受监视的域控制器，来轻松比较某个域控制器的性能。 此外，你还可以看到并排放置的各种性能计数器，这对排查环境中出现的问题很有帮助。

![监视](./media/active-directory-aadconnect-health/aadconnect-health-adds-monitoring.png)

默认情况下，我们已经预先选择四个性能计数器；但是，你可以通过单击筛选命令并选择或取消选择任何所需的性能计数器，来添加其他计数器。 此外，可以双击某个性能计数器图表打开一个新的边栏选项卡，其中包含每个受监视域控制器的数据点。

## <a name="related-links"></a>相关链接
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health 代理安装](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [在 AD FS 中使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [使用用于同步的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health 常见问题](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 版本历史记录](active-directory-aadconnect-health-version-history.md)




<!--HONumber=Jan17_HO3-->


