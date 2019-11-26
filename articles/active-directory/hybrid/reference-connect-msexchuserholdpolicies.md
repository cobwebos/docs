---
title: Azure AD Connect：msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies | Microsoft Docs
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
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213078"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies
以下参考文档介绍了 Exchange 使用的这些属性，以及编辑默认同步规则的适当方式。

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>什么是 msExchUserHoldPolicies 和 cloudMsExchUserHoldPolicies？
有两种类型的[保留](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019)可用于 Exchange 服务器：诉讼持有和就地保留。 启用诉讼保留后，所有邮箱和项都会保留。  就地保留仅保留符合你使用 In-Place eDiscovery 工具定义的搜索查询条件的项。

MsExchUserHoldPolcies 和 cloudMsExchUserHoldPolicies 属性允许本地 AD 与 Azure AD 根据用户使用的是本地 Exchange 还是联机 Exchange，来确定哪些用户处于保留状态。

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies 同步流
默认情况下，MsExchUserHoldPolcies 将由 Azure AD Connect 直接同步到 Metaverse 中的 msExchUserHoldPolicies 属性，然后同步到 Azure AD 中的 msExchUserHoldPolices 属性

下表描述了该流：

从本地 Active Directory 入站：

|Active Directory 属性|属性名称|流类型|Metaverse 属性|同步规则|
|-----|-----|-----|-----|-----|
|本地 Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolices|In from AD - User Exchange|

出站到 Azure AD：

|Metaverse 属性|属性名称|流类型|Azure AD 属性|同步规则|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|直接|msExchUserHoldPolicies|Out to AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies 同步流
默认情况下，cloudMsExchUserHoldPolicies 由 Azure AD Connect 直接同步到 Metaverse 中的 cloudMsExchUserHoldPolicies 属性。 如果 Metaverse 中的 msExchUserHoldPolices 不为 null，该属性将流出到 Active Directory。

下表描述了该流：

从 Azure AD 入站：

|Active Directory 属性|属性名称|流类型|Metaverse 属性|同步规则|
|-----|-----|-----|-----|-----|
|本地 Active Directory|cloudMsExchUserHoldPolicies|直接|cloudMsExchUserHoldPolicies|In from AAD - User Exchange|

出站到本地 Active Directory：

|Metaverse 属性|属性名称|流类型|Azure AD 属性|同步规则|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF(NOT NULL)|msExchUserHoldPolicies|Out to AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>有关属性行为的信息
msExchangeUserHoldPolicies 是单机构属性。  可以针对本地目录或云目录中的对象（在本例中为用户对象）设置单机构属性。  “启动机构”规则规定，如果属性已从本地同步，则不允许 Azure AD 更新此属性。

若要允许用户针对云中的用户对象设置保留策略，请使用 cloudMSExchangeUserHoldPolicies 属性。 之所以使用此属性，是因为 Azure AD 无法根据上述规则直接设置 msExchangeUserHoldPolicies。  如果 msExchangeUserHoldPolicies 不为 null，则此属性将同步回到本地目录，并替换 msExchangeUserHoldPolicies 的当前值。

例如，在某些情况下，如果两者同时在本地和 Azure 中发生更改，这可能会导致一些问题。  

## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
