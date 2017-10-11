---
title: "Azure Active Directory B2C：区域可用性和数据驻留 | Microsoft Docs"
description: "有关 Azure Active Directory B2C 租户类型的主题"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: gsacavdm
ms.openlocfilehash: facd66f0324e382ea7609a035de8129ba433846f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C：区域可用性和数据驻留
区域可用性和数据驻留是两个截然不同的概念，它们以不同方式应用到 Azure AD B2C 和 Azure 的其余部分。 本文介绍这两个概念之间的区别，并对它们应用到 Azure 和 Azure AD B2C 的方式进行比较。

## <a name="summary"></a>摘要
Azure AD B2C **可在全球范围通用**，并且还可选择**在美国或欧洲进行数据驻留**。

## <a name="concepts"></a>概念
* **区域可用性**是指服务适用的地区。
* **数据驻留**是指存储用户数据的位置。

## <a name="region-availability"></a>上市区域
Azure AD B2C 可通过 Azure 公有云在全球通用。 

这与大多数其他 Azure 服务所遵循的模式不同，后者的可用性与数据驻留的可用性结合在一起。 可在 Azure 的[可用产品(按区域)](https://azure.microsoft.com/regions/services/)页面和 [Active Directory B2C 定价计算器](https://azure.microsoft.com/pricing/details/active-directory-b2c/)中查看相关示例。

## <a name="data-residency"></a>数据驻留
Azure AD B2C 将用户数据存储在美国或欧洲。

数据驻留取决于[创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)时选择的国家/地区。

![预览租户的屏幕截图](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

在美国驻留数据适用于以下国家/地区：

> 美国、加拿大、哥斯达黎加、多米尼加共和国、萨尔瓦多、危地马拉、墨西哥、巴拿马、波多黎各以及特立尼达和多巴哥

在欧洲驻留数据适用于以下国家/地区：

> 阿尔及利亚、奥地利、阿塞拜疆、巴林、白俄罗斯、比利时、保加利亚、克罗地亚、塞浦路斯、捷克共和国、丹麦、埃及、爱沙尼亚、希腊、匈牙利、冰岛、爱尔兰、以色列、意大利、约旦、哈萨克斯坦、肯尼亚、科威特、拉脱维亚、黎巴嫩、列支敦士登、立陶宛、卢森堡、前南斯拉夫马其顿共和国、马耳他、黑山、摩洛哥、尼日利亚、挪威、阿曼、巴基斯坦、波兰、葡萄牙、卡塔尔、罗马尼亚、俄罗斯、沙特阿拉伯、塞尔维亚、斯洛伐克、斯洛文尼亚、南非、西班牙、瑞典、瑞士、突尼斯、土耳其、乌克兰、阿拉伯联合酋长国以及英国。

当前正向该列表中加入其他国家/地区。  目前，仍然可以通过选择上述任何国家/地区来使用 Azure AD B2C。

> 阿富汗、阿根廷、澳大利亚、巴西、智利、哥伦比亚、厄瓜多尔、香港特别行政区、印度、印度尼西亚、伊拉克、日本、韩国、马来西亚、新西兰、巴拉圭、秘鲁、菲律宾、新加坡、斯里兰卡、台湾、泰国、乌拉圭以及委内瑞拉。

## <a name="preview-tenant"></a>预览租户
如果在 Azure AD B2C 预览期间创建了 B2C 租户，那么“租户类型”可能会显示为“预览租户”。 如果是这种情况，必须只能将租户用于开发和测试目的，而不能用于生产应用。

> [!IMPORTANT]
> 没有从预览 B2C 租户到生产规模 B2C 租户的迁移路径。 请注意，删除预览 B2C 租户并重新创建具有相同域名的生产规模 B2C 租户时，会发生已知问题。 必须创建具有不同域名的生产规模 B2C 租户。


![预览租户的屏幕截图](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
