---
title: "Azure Active Directory B2C：生产规模与预览 B2C 租户 | Microsoft Docs"
description: "有关 Azure Active Directory B2C 租户类型的主题"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 4b13c040a15bef2f04d2cd2126e2270d061898bd
ms.openlocfilehash: a37992cd2bfe346fd171bde15b6180c56527289b


---
# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure Active Directory B2C：生产规模与预览 B2C 租户
如果计划在 Azure Active Directory (Azure AD) B2C 上编写生产应用，则需要确保拥有适当的租户“类型”才能继续进行。 若要查看拥有的租户类型，请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)，并查看“租户类型”。

## <a name="summary"></a>摘要
Azure AD B2C 仅在北美的“生产规模”B2C租户上支持生产应用。

| 租户类型 | 国家/地区 | 已正式推出？ |
| --- | --- | --- |
| **生产规模租户** |北美国家/地区 |是 |
| **生产规模租户** |所有国家/地区（除北美以外） |否 |
| **预览租户** |所有国家/地区 |否 |

> [!NOTE]
> Azure AD B2C 租户（用于使用者）当前在 Azure AD 租户（用于员工）可用的几个国家或地区中不可用。 有关详细信息，请阅读以下部分。
> 
> 

## <a name="production-scale-b2c-tenant-in-north-america"></a>北美的生产规模 B2C 租户
如果在北美（即在以下国家或地区之一）[创建了 B2C 租户](active-directory-b2c-get-started.md)：美国、加拿大、哥斯达黎加、多米尼加共和国、萨尔瓦多、危地马拉、墨西哥、巴拿马、波多黎各以及特里尼达和多巴哥，并且在 B2C 管理 UI 上的“租户类型”显示为“生产规模”，则租户可以用于生产应用。

> [!NOTE]
> 生产规模租户能扩大到每个租户数亿个使用者标识。
> 
> 

![生产规模租户的屏幕截图](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>任何国家/地区中的预览 B2C 租户
如果在 Azure AD B2C 预览期间创建了 B2C 租户，那么“租户类型”可能会显示为“预览租户”。 如果是这种情况，必须只能将租户用于开发和测试目的，而不能用于生产应用。

> [!IMPORTANT]
> 没有从预览 B2C 租户到生产规模 B2C 租户的迁移路径。 请注意，删除预览 B2C 租户并重新创建具有相同域名的生产规模 B2C 租户时，会发生已知问题。 必须创建具有不同域名的生产规模 B2C 租户。
> 
> 

![预览租户的屏幕截图](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>北美以外的生产规模的 B2C 租户
目前未在北美以外的地区正式推出 Azure AD B2C。 但是，可以在以下国家或地区之一创建和使用用于开发和测试目的的生产规模租户：阿尔及利亚、奥地利、阿塞拜疆、巴林、白俄罗斯、比利时、保加利亚、克罗地亚、塞浦路斯、捷克共和国、丹麦、埃及、爱沙尼亚、希腊、匈牙利、冰岛、爱尔兰、以色列、意大利、约旦、哈萨克斯坦、肯尼亚、科威特、拉脱维亚、黎巴嫩、列支敦士登、立陶宛、卢森堡、前南斯拉夫马其顿共和国、马耳他、黑山、摩洛哥、尼日利亚、挪威、阿曼、巴基斯坦、波兰、葡萄牙、卡塔尔、罗马尼亚、俄罗斯、沙特阿拉伯、塞尔维亚、斯洛伐克、斯洛文尼亚、南非、西班牙、瑞典、瑞士、突尼斯、土耳其、乌克兰、阿拉伯联合酋长国以及英国。

一旦 Azure AD B2C 在上述国家或地区宣布正式推出后，就可以继续使用这些生产规模租户，并与生产应用一起上线，而不会丢失任何数据。

## <a name="availability-of-b2c-tenants"></a>B2C 租户的可用性
B2C 租户目前并未在以下国家或地区推出：阿富汗、阿根廷、澳大利亚、巴西、智利、哥伦比亚、厄瓜多尔、香港特别行政区、印度、印度尼西亚、伊拉克、日本、韩国、马来西亚、新西兰、巴拉圭、秘鲁、菲律宾、新加坡、斯里兰卡、台湾、泰国、乌拉圭以及委内瑞拉。 我们计划在未来纳入这些国家或地区。




<!--HONumber=Dec16_HO5-->


