---
title: 区域可用性和数据驻留
titleSuffix: Azure AD B2C
description: 区域可用性、数据驻留以及有关 Azure Active Directory B2C 预览版租户的信息。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: f8c6f7daecd38babaa4f2961d04a6cd4c3b4dbed
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840551"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C：区域可用性和数据驻留

区域可用性和数据驻留是两个截然不同的概念，它们以不同方式应用到 Azure AD B2C 和 Azure 的其余部分。 本文介绍这两个概念之间的区别，并比较它们如何应用于 Azure 与 Azure AD B2C。

Azure AD B2C 在**全球范围内公开提供**，在**美国、欧洲或亚太**中提供**数据驻留**选项。

[区域可用性](#region-availability)是指服务适用的地区。

[数据驻留](#data-residency)是指存储用户数据的位置。

## <a name="region-availability"></a>上市区域

Azure AD B2C 可通过 Azure 公有云在全球通用。

这不同于模型，然后是大多数其他 Azure 服务，通常*可以使用**数据驻留*。 可在 Azure 的[可用产品(按区域)](https://azure.microsoft.com/regions/services/)页面和 [Active Directory B2C 定价计算器](https://azure.microsoft.com/pricing/details/active-directory-b2c/)中查看相关示例。

## <a name="data-residency"></a>数据驻留

Azure AD B2C 将用户数据存储在美国、欧洲或亚太区域中。

数据驻留取决于你在 [创建 Azure AD B2C 租户](tutorial-create-tenant.md)时选择的国家/地区：

![创建租户窗体的屏幕截图，选择国家或地区。](./media/data-residency/data-residency-b2c-tenant.png)

数据驻留在以下国家/地区的 **美国** 中：

> 美国 (美国) 、加拿大 (CA) 、哥斯达黎加 (CR) 、多米尼加共和国 () 、萨尔瓦多 (SV) 、危地马拉 (GT) 、墨西哥 (MX) 、巴拿马 (PA) 、波多黎各 (PR) 和特立尼达 &

对于以下国家/地区，数据驻留在 **欧洲** ：

> 阿尔及利亚 (DZ) ，奥地利)  (，x： x (AZ) ，巴林 (BH) ，白俄罗斯 (，) ，比利时 (为) 、保加利亚 (BG) 、克罗地亚 (HR) 、塞浦路斯 (CY) 、捷克共和国 (CZ) 、丹麦 () 、芬兰 (德国 (DE) ，希腊 (GR) ，匈牙利 (HU) ，冰岛 () 、爱尔兰 (IE) 、以色列 (，意大利)  () 、约旦 (JO) 、哈萨克斯坦 (KZ) 、肯尼亚 (LV) 、科威特 () 、拉脱维亚 (LV) 、立陶宛 (LT) ，卢森堡 (LU) ，北马其顿共和国 (ML) ，马耳他 (MT) 、黑山 (ME) 、摩洛哥 (MA) 、荷兰 (NL) 、尼日利亚 (NG) 、挪威 (NO) 、阿曼 (OM) 、葡萄牙 (PT) 、和俄罗斯 (RU) ，沙特阿拉伯 (SA) ，塞尔维亚 (RS) ，斯洛伐克 (SK) ，斯洛文尼亚 (ST) ，南非 (，西班牙) ES (，瑞典) SE (，瑞士) CH (，突尼斯) TN (，土耳其) （AE (和英国) GB (

数据驻留在以下国家/地区 **亚太** 中：

> 阿富汗 (AF) 、中国香港特别行政区 (HK) 、印度 (在) 、印度尼西亚 (ID) 、日本 (日本) 、韩国 (KR) 、 () 、菲律宾 (、) 、台湾 (幼圆) 和泰国 () 。

以下国家/地区正在添加到列表中。 目前，仍然可以通过选择上述任何国家/地区来使用 Azure AD B2C。

> 阿根廷、澳大利亚、巴西、智利、哥伦比亚、厄瓜多尔、伊拉克、新西兰、巴拉圭、秘鲁、乌拉圭和委内瑞拉。

## <a name="preview-tenant"></a>预览租户

如果你在 Azure AD B2c 预览版期间创建了 B2C 租户，则你的 **租户类型** 可能显示为 **预览版租户**。

如果是这种情况，则必须仅将租户用于开发和测试目的。 不要对生产应用程序使用预览版租户。

预览版 B2C 租户与生产规模 B2C 租户之间**没有迁移路径**。 必须为生产应用程序创建新的 B2C 租户。

删除预览版 B2C 租户并创建具有相同域名的生产规模 B2C 租户时，存在已知问题。 *必须创建具有不同域名的生产规模 B2C 租户*。

![作为预览租户的租户类型的屏幕截图。](./media/data-residency/preview-b2c-tenant.png)