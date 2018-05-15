---
title: 按国家/地区限制 Azure CDN 内容 | Microsoft Docs
description: 了解如何使用地区筛选功能限制对 Azure CDN 内容的访问。
services: cdn
documentationcenter: ''
author: lichard
manager: akucer
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: bb757ab115d03ab04dac4468d23f446696a971a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="restrict-azure-cdn-content-by-country"></a>按国家/地区限制 Azure CDN 内容

## <a name="overview"></a>概述
当用户请求内容时，默认情况下，系统会提交该内容，不管用户是从何处提交该请求。 在某些情况下，可能需要按国家/地区限制内容访问。 本文说明了如何使用“地区筛选”功能来配置服务，以便按国家/地区允许或阻止访问。

> [!IMPORTANT]
> 所有 Azure CDN 产品都提供相同的地区筛选功能，但在其支持的国家/地区代码方面有些许差别。 有关不同之处的链接，请参阅步骤 3。


有关配置这种类型的限制所适用的注意事项的信息，请参阅[注意事项](cdn-restrict-access-by-country.md#considerations)。  

![国家/地区筛选](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>步骤 1：定义目录路径
> [!IMPORTANT]
> **Microsoft 的 Azure CDN 标准版**配置文件不支持基于路径的地区筛选。
>


在门户内选择终结点，并在左侧导航中找到“地区筛选”选项卡，以查找此功能。

在配置国家/地区筛选器时，必须指定允许或拒绝用户访问的位置的相对路径。 可使用正斜杠 (/) 对所有文件应用地区筛选，也可通过指定目录路径 */pictures/* 对选定文件夹应用该筛选。 还可通过指定单个文件并省略尾部斜杠 */pictures/city.png* 将地区筛选应用到该文件。

目录路径筛选器示例：

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>步骤 2：定义操作：阻止或允许
**阻止：** 来自指定国家/地区的用户会被拒绝通过该递归路径访问所请求的资产。 如果没有针对该位置配置其他国家/地区筛选选项，则所有其他用户都允许进行访问。

**允许：** 仅允许来自指定国家/地区的用户通过该递归路径访问所请求的资产。

## <a name="step-3-define-the-countries"></a>步骤 3：定义国家/地区
针对该路径，选择要阻止或允许的国家/地区。 

例如，如果规则阻止 /Photos/Strasbourg/，则会筛选下述文件：

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>国家/地区代码
地区筛选功能使用国家/地区代码来定义相关国家/地区，这样系统就可以允许或阻止这些国家/地区发出的针对受保护目录的请求。 虽然所有 Azure CDN 产品都提供相同的地区筛选功能，但在其支持的国家/地区代码方面有些许差别。 有关详细信息，请参阅 [Azure CDN 国家/地区代码](https://msdn.microsoft.com/library/mt761717.aspx)。 

## <a name="considerations"></a>注意事项
* 对国家/地区筛选配置的更改不会立即生效：
   * 对于 **Microsoft 的 Azure CDN 标准版**配置文件，传播通常可在 10 分钟内完成。 
   * 对于 **Akamai 的 Azure CDN 标准版**配置文件，传播通常可在一分钟内完成。 
   * 对于 **Verizon 的 Azure CDN 标准版**和 **Verizon 的 Azure CDN 高级版**配置文件，传播通常可在 90 分钟内完成。  
* 此功能不支持通配符（例如“*”）。
* 与相对路径关联的地区筛选配置以递归方式应用于该路径。
* 只能将一条规则应用于同一相对路径（不能创建多个指向同一相对路径的国家/地区筛选器）。 但是，一个文件夹可以有多个国家/地区筛选器。 这是因为国家/地区筛选器的递归性质。 换言之，即使某个文件夹此前已配置过，也可以向该文件夹的子文件夹分配其他国家/地区筛选器。

