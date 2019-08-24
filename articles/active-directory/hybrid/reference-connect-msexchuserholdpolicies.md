---
title: 'Azure AD Connect: msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies |Microsoft Docs'
description: 本主题介绍 msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies 属性的属性行为
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d371ba2a09dda933bf14208a00535b757afea85
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014329"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect-msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies
以下参考文档介绍了 Exchange 使用的这些属性, 以及编辑默认同步规则的正确方法。

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>什么是 msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies？
有两种类型的[保留](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019)可用于 Exchange 服务器:诉讼持有和就地保留。 启用诉讼持有后, 所有邮件都将置于保持状态。  就地保留用于仅保留满足你使用就地电子数据展示工具定义的搜索查询条件的那些项。

MsExchUserHoldPolcies 和 cloudMsExchUserHoldPolicies 属性允许本地 AD 和 Azure AD 确定哪些用户处于保留状态, 具体取决于他们使用的是本地 Exchange 还是 Exchange 联机。

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies 同步流
默认情况下, MsExchUserHoldPolcies 通过 Azure AD Connect 直接与元节中的 msExchUserHoldPolicies 属性进行同步, 然后将其同步到中的 msExchUserHoldPolices 特性 Azure AD

下表描述了流:

本地 Active Directory 中的入站:

|Active Directory 特性|属性名|流类型|元节特性|同步规则|
|-----|-----|-----|-----|-----|
|本地 Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolices|In from AD - User Exchange|

出站到 Azure AD:

|元节特性|属性名|流类型|Azure AD 特性|同步规则|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolicies|Out to AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies 同步流
默认情况下, cloudMsExchUserHoldPolicies 通过 Azure AD Connect 直接同步到元节中的 cloudMsExchUserHoldPolicies 属性。 如果元节中的 msExchUserHoldPolices 不为 null, 则流出的属性将 Active Directory。

下表描述了流:

Azure AD 中的入站:

|Active Directory 特性|属性名|流类型|元节特性|同步规则|
|-----|-----|-----|-----|-----|
|本地 Active Directory|cloudMsExchUserHoldPolicies|直接|cloudMsExchUserHoldPolicies|In from AAD-User Exchange|

出站到本地 Active Directory:

|元节特性|属性名|流类型|Azure AD 特性|同步规则|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NOT NULL)|msExchUserHoldPolicies|Out to AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>有关属性行为的信息
MsExchangeUserHoldPolicies 是一个权威属性。  可以在本地目录或云目录中的对象 (在此例中为用户对象) 上设置单个颁发机构属性。  授权开始规则规定, 如果该属性是从本地同步的, 则不允许 Azure AD 更新此属性。

若要允许用户设置云中用户对象的保留策略, 请使用 cloudMSExchangeUserHoldPolicies 属性。 使用此属性是因为 Azure AD 无法根据上述规则直接设置 msExchangeUserHoldPolicies。  如果 msExchangeUserHoldPolicies 不为 null 并替换 msExchangeUserHoldPolicies 的当前值, 则此属性将同步回到本地目录。

例如, 在某些情况下, 如果两者同时在本地和 Azure 中发生更改, 这可能会导致一些问题。  

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
