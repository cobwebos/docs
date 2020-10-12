---
title: 适用于澳大利亚和新西兰的客户数据存储-Azure AD
description: 了解 Azure Active Directory 为澳大利亚和新西兰的客户存储客户相关数据的位置。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 7/21/2020
ms.custom: it-pro, seodec18, references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bbd31bf47c7ce7ae75a6934da0ea769698ef187
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565100"
---
# <a name="customer-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>适用于澳大利亚和新西兰的客户数据存储 Azure Active Directory

Azure Active Directory (Azure AD) 根据你在注册 Microsoft Online service 时提供的国家/地区将客户数据存储在地理位置。 Microsoft Online services 包括 Microsoft 365 和 Azure。 

有关 Azure AD 和其他 Microsoft 服务数据的位置的信息，请参阅 Microsoft 信任中心的 " [数据的位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) " 部分。

从2020年2月26日开始，Microsoft 开始为新租户存储 Azure AD 的客户数据，这些数据使用澳大利亚或新西兰的新租户。 在5月1日到2020年10月 2020 31 日之间，Microsoft 会将具有澳大利亚或新西兰帐单地址的现有租户迁移到澳大利亚数据中心，而无需任何客户操作。 迁移过程不会对客户造成任何停机，也不会在迁移过程中影响租户的任何功能。

此外，某些 Azure AD 功能尚不支持在澳大利亚存储客户数据。 有关特定功能信息，请参阅 [Azure AD 数据图](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)。 例如，Microsoft Azure 多重身份验证将客户数据存储在美国，并对其进行全局处理。 [有关 Azure 多重身份验证，请参阅数据驻留和客户数据](../authentication/concept-mfa-data-residency.md)。

> [!NOTE]
> 与 Azure AD 集成的 Microsoft 产品、服务和第三方应用程序可以访问客户数据。 评估你使用的每个产品、服务和应用程序，以确定此特定产品、服务和应用程序如何处理客户数据，以及它们是否满足你公司的数据存储要求。 有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。