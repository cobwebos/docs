---
title: 区域可用性和数据驻留
titleSuffix: Azure AD B2C
description: 区域可用性、数据驻留以及有关 Azure Active Directory B2C 预览版租户的信息。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1b09eb85df6748fed042731ac90ebbf20c65b702
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950504"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C：区域可用性和数据驻留

区域可用性和数据驻留是两个截然不同的概念，它们以不同方式应用到 Azure AD B2C 和 Azure 的其余部分。 本文介绍这两个概念之间的区别，并比较它们如何应用于 Azure 与 Azure AD B2C。

Azure AD B2C 在**全球范围内公开提供**，在**美国、欧洲或亚太**中提供**数据驻留**选项。

[区域可用性](#region-availability)是指服务适用的地区。

[数据驻留](#data-residency)是指存储用户数据的位置。

## <a name="region-availability"></a>适用区域

Azure AD B2C 可通过 Azure 公有云在全球通用。

这不同于模型，然后是大多数其他 Azure 服务，通常*可以使用* *数据驻留*。 可在 Azure 的[可用产品(按区域)](https://azure.microsoft.com/regions/services/)页面和 [Active Directory B2C 定价计算器](https://azure.microsoft.com/pricing/details/active-directory-b2c/)中查看相关示例。

## <a name="data-residency"></a>数据驻留

Azure AD B2C 将用户数据存储在美国、欧洲或亚太区域中。

数据驻留取决于你在[创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)时选择的国家/地区：

![预览租户的屏幕截图](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

数据驻留在以下国家/地区的**美国**中：

> 美国、加拿大、哥斯达黎加、多米尼加共和国、萨尔瓦多、危地马拉、墨西哥、巴拿马、波多黎各以及特立尼达和多巴哥

对于以下国家/地区，数据驻留在**欧洲**：

> 阿尔及利亚、奥地利、阿塞拜疆、巴林、白俄罗斯、比利时、保加利亚、克罗地亚、塞浦路斯、捷克共和国、丹麦、埃及、爱沙尼亚、芬兰、法国、德国、希腊、匈牙利、冰岛、爱尔兰、以色列、芬兰、约旦、哈萨克斯坦、肯尼亚、科威特、拉脱维亚、黎巴嫩、列支敦士登、立陶宛、卢森堡、北欧马其顿共和国、马耳他、黑山、摩洛哥、荷兰、尼日利亚、挪威、阿曼、巴基斯坦、波兰、葡萄牙、卡塔尔、罗马尼亚、俄罗斯、沙特阿拉伯、塞尔维亚、斯洛伐克、斯洛文尼亚、南非、西班牙、瑞典、瑞士，突尼斯，土耳其，乌克兰，阿拉伯联合酋长国和英国。

数据驻留在以下国家/地区**亚太**中：

> 阿富汗、香港特别行政区、印度、印度尼西亚、日本、韩国、马来西亚、菲律宾、新加坡、斯里兰卡、台湾和泰国。

以下国家/地区正在添加到列表中。 目前，仍然可以通过选择上述任何国家/地区来使用 Azure AD B2C。

> 阿根廷、澳大利亚、巴西、智利、哥伦比亚、厄瓜多尔、伊拉克、新西兰、巴拉圭、秘鲁、乌拉圭和委内瑞拉。

## <a name="preview-tenant"></a>预览租户

如果你在 Azure AD B2c 预览版期间创建了 B2C 租户，则你的**租户类型**可能显示为**预览版租户**。

如果是这种情况，则必须仅将租户用于开发和测试目的。 不要对生产应用程序使用预览版租户。

预览版 B2C 租户与生产规模 B2C 租户之间**没有迁移路径**。 必须为生产应用程序创建新的 B2C 租户。

删除预览版 B2C 租户并创建具有相同域名的生产规模 B2C 租户时，存在已知问题。 *必须创建具有不同域名的生产规模 B2C 租户*。

![预览租户的屏幕截图](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)