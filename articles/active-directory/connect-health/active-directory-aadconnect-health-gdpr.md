---
title: Azure AD Connect Health 和用户隐私 | Microsoft Docs
description: 本文档介绍 Azure AD Connect Health 的用户隐私。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: billmath
ms.openlocfilehash: 5fedbac439636b56da217e7babd30820bce7b342
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38442560"
---
# <a name="user-privacy-and-azure-ad-connect-health"></a>用户隐私和 Azure AD Connect Health 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>本文介绍 Azure AD Connect Health 和用户隐私。  有关 Azure AD Connect 和用户隐私的信息，请参阅[此处](../../active-directory/connect/active-directory-aadconnect-gdpr.md)的文章。

## <a name="user-privacy-classification"></a>用户隐私分类
Azure AD Connect Health 属于 GDPR 的数据处理器类别。 该服务作为数据处理器管道，向关键合作伙伴和最终客户提供数据处理服务。 Azure AD Connect Health 不生成用户数据，且不独立控制要收集的个人数据类型及其使用方式。 Azure AD Connect Health 基于现有本地数据进行数据检索、聚合、分析和报告。 

## <a name="data-retention-policy"></a>数据保留策略
Azure AD Connect Health 不生成报告、不进行分析，也不提供 30 天之前的见解。 因此，Azure AD Connect Health 不存储、处理或保留任何 30 天以前的数据。 此设计符合 GDPR 法规要求、Microsoft 隐私符合性规定和 Azure AD 数据保留策略。 

连续 30 天以上显示活动的“运行状况服务数据不是最新”**错误**警报的服务器表明在该时间范围内没有数据到达 Connect Health。 这些服务器将被禁用，并且不显示在 Connect Health 门户中。 若要重新启用这些服务器，必须卸载并[重新安装运行状况代理](active-directory-aadconnect-health-agent-install.md)。 请注意，这不适用于具有相同警报类型的**警告**。 警告指示向你发出警报的服务器中缺少部分数据。 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>在 Azure AD Connect Health 中禁用数据收集和监视
借助 Azure AD Connect Health，可针对所监视的单独服务器或所监视服务的实例停止数据收集功能。 例如，可针对使用 Azure AD Connect Health 进行监视的单独 ADFS（Active Directory 联合身份验证服务）服务器停止数据收集功能。 还可针对当前通过 Azure AD Connect Health 监视的整个 ADFS 实例停止数据收集功能。 选择停止此功能后，会在停止数据收集后从 Azure AD Connect Health 门户删除相应的服务器。 

>[!IMPORTANT]
> 需在 RBAC 具备 Azure AD 全局管理员特权或参与者角色，才能从 Azure AD Connect Health 中删除所监视的服务器。
>
> 从 Azure AD Connect Health 删除服务器或服务实例后，操作不可撤消。 

### <a name="what-to-expect"></a>要发生的情况
如果针对单个监视的服务器或所监视服务的实例停止数据收集和监视功能，请注意以下情况：

- 删除所监视服务的实例后，会从门户的 Azure AD Connect Health 监视服务列表中删除此实例。 
- 删除所监视的服务器或所监视服务的实例后，不会从服务器中卸载或删除运行状况代理。 运行状况代理已配置为不发送数据至 Azure AD Connect Health。 需要在之前监视的服务器上手动卸载运行状况代理。
- 如果在执行此步骤之前未卸载 Health 代理，则可能会在与 Health 代理相关的服务器上看到错误事件。
- 按照 Microsoft Azure 数据保留策略删除所监视服务的实例中的所有数据。

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>对所监视服务的实例禁用数据收集和监视
请参阅[如何删除 Azure AD Connect Health 中的服务实例](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service)。

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>对所监视服务器禁用数据收集和监视
请参阅[如何从 Azure AD Connect Health 中删除服务器](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service)。

### <a name="disable-data-collection-and-monitoring-for-all-monitored-services-in-azure-ad-connect-health"></a>在 Azure AD Connect Health 中对所有受监视服务禁用数据收集和监视
Azure AD Connect Health 还提供对租户中的**所有**已注册服务停止收集数据的选项。 建议在慎重考虑并得到所有全局管理员的确认之后再执行该操作。 一旦开始此过程，Connect Health 服务就会停止接收、处理和报告所有服务的任何数据。 Connect Health 服务中现有数据的保留期不超过 30 天。
如果你想停止特定服务器的数据收集，请按照删除特定服务器的步骤进行操作。 若要停止租户范围内的数据收集，请按照以下步骤停止数据收集并删除租户的所有服务。

1.  在主要边栏选项卡的配置下单击“常规设置”。 
2.  单击边栏选项卡顶部的“停止收集数据”按钮。 一旦开始此过程，系统就会禁用租户配置设置的其他选项。  
 
 ![停止收集数据](./media/active-directory-aadconnect-health-gdpr/gdpr4.png)
  
3.  确定因停止收集数据而受影响的已载入服务的列表。 
4.  输入确切的租户名称以启用“删除”操作按钮
5.  单击“删除”触发删除所有服务的操作。 Connect Health 将停止接收、处理和报告从已载入服务发送的任何数据。 整个过程可能需要长达 24 小时。 注意此步骤是不可逆的。 
6.  完成此过程后，Connect Health 不会再显示任何已注册的服务。 

 ![停止收集数据之后](./media/active-directory-aadconnect-health-gdpr/gdpr5.png)

## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>重新在 Azure AD Connect Health 中启用数据收集和监视
对于之前已删除的被监视服务，若要在 Azure AD Connect Health 中重新启用监视，必须在所有服务器上卸载再[重新安装运行状况代理r](active-directory-aadconnect-health-agent-install.md)。

### <a name="re-enable-data-collection-and-monitoring-for-all-monitored-services"></a>对所有受监视服务重新启用数据收集和监视

可以在 Azure AD Connect Health 中恢复租户范围内的数据收集。 建议在慎重考虑并得到所有全局管理员的确认之后再执行该操作。

>[!IMPORTANT]
> 禁用操作 24 小时后可执行以下步骤。
> 启用数据收集后，Connect Health 中提供的见解和监测数据不会显示之前收集的任何旧数据。 

1.  在主要边栏选项卡的配置下单击“常规设置”。 
2.  单击边栏选项卡顶部的“启用数据收集”按钮。 
 
 ![启用数据收集](./media/active-directory-aadconnect-health-gdpr/gdpr6.png)
 
3.  输入确切的租户名称以激活“启用”按钮。
4.  单击“启用”按钮以便授权在 Connect Health 服务中收集数据。 系统将立即应用此更改。 
5.  按照[安装过程](active-directory-aadconnect-health-agent-install.md)在要监视的服务器中重新安装代理，服务将显示在门户中。  


## <a name="next-steps"></a>后续步骤
* [查看信任中心上的 Microsoft 隐私策略](https://www.microsoft.com/trustcenter)
* [Azure AD Connect 和用户隐私](../../active-directory/connect/active-directory-aadconnect-gdpr.md)

