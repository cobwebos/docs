---
title: "按国家/地区限制对 Azure CDN 内容的访问 | Microsoft Docs"
description: "了解如何使用地区筛选功能限制对 Azure CDN 内容的访问。"
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 20943ac8-9dce-4cf2-85ed-e8362750aa63
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: 2ee72c31072fb5201e057f4c3567d33ce53e4fbc
ms.openlocfilehash: 1a1cf8cea1b321bb4e758e9ad06e42da59d3bcb4


---
# <a name="restrict-access-to-your-content-by-country---verizon"></a>按国家/地区限制内容访问 - Verizon
> [!div class="op_single_selector"]
> * [Verizon](cdn-restrict-access-by-country.md)
> * [Akamai 标准](cdn-restrict-access-by-country-akamai.md)
> 
> 

## <a name="overview"></a>概述
当用户请求你的内容时，默认情况下，系统会提交该内容，不管用户是从何处提交该请求。 在某些情况下，你可能需要按国家/地区限制内容访问。 本主题说明如何使用“地区筛选”功能来配置服务，以便按国家/地区允许或阻止访问。

> [!IMPORTANT]
> Verizon 和 Akamai 产品提供相同的地区筛选功能，但用户界面有所不同。 本文档介绍**来自 Verizon 的标准 Azure CDN** 和**来自 Verizon 的高级 Azure CDN** 的界面。 有关使用**来自 Akamai 的标准 Azure CDN** 进行地区筛选的信息，请参阅[按国家/地区限制内容访问 - Akamai](cdn-restrict-access-by-country-akamai.md)。
> 
> 

有关配置这种类型的限制所适用的注意事项的信息，请参阅本主题结尾的“[注意事项](cdn-restrict-access-by-country.md#considerations)”部分。  

> [!NOTE]
> 一旦设置了配置，它将应用于此 Azure CDN 配置文件中的所有**来自 Verizon 的 Azure CDN** 终结点。
> 
> 

![国家/地区筛选](./media/cdn-filtering/cdn-country-filtering.png)

## <a name="step-1-define-the-directory-path"></a>步骤 1：定义目录路径
在配置国家/地区筛选器时，必须指定允许或拒绝用户访问的位置的相对路径。 你可以使用“/”对所有文件应用国家/地区筛选，也可以通过指定目录路径对选定文件夹应用该筛选。

目录路径筛选器示例：

    /                                 
    /Photos/
    /Photos/Strasbourg

## <a name="step-2-define-the-action-block-or-allow"></a>步骤 2：定义操作：阻止或允许
**阻止：**来自指定国家/地区的用户将被拒绝通过该递归路径访问所请求的资产。 如果没有针对该位置配置其他国家/地区筛选选项，则所有其他用户都允许进行访问。

**允许：**仅允许来自指定国家/地区的用户通过该递归路径访问所请求的资产。

## <a name="step-3-define-the-countries"></a>步骤 3：定义国家/地区
针对该路径，选择你想要阻止或允许的国家/地区。 有关详细信息，请参阅[来自 Verizon 的 Azure CDN 国家/地区代码](https://msdn.microsoft.com/library/mt761717.aspx)。

例如，如果规则阻止 /Photos/Strasbourg/，则会筛选下述文件：

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


## <a name="country-codes"></a>国家/地区代码
“地区筛选”功能使用国家/地区代码来定义相关国家/地区，这样系统就可以允许或阻止这些国家/地区发出的针对受保护目录的请求。 你可以在[来自 Verizon 的 Azure CDN 国家/地区代码](https://msdn.microsoft.com/library/mt761717.aspx)中可以找到这些国家/地区代码。 如果指定“EU”（欧洲）或“AP”（亚太），系统会阻止或允许该区域任何国家/地区的 IP 地址。

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>注意事项
* 可能需要长达 90 分钟的时间，你对国家/地区筛选配置的更改才会生效。
* 此功能不支持通配符（例如“*”）。
* 与相对路径关联的国家/地区筛选配置将以递归方式应用于该路径。
* 只能将一条规则应用于同一相对路径（不能创建多个指向同一相对路径的国家/地区筛选器）。 但是，一个文件夹可以有多个国家/地区筛选器。 这是因为国家/地区筛选器的递归性质。 换言之，即使某个文件夹此前已配置过，你也可以向该文件夹的子文件夹分配其他国家/地区筛选器。




<!--HONumber=Nov16_HO3-->


