---
title: 按国家/地区限制 Azure CDN 内容 |微软文档
description: 了解如何通过使用地理筛选功能限制按国家/地区访问 Azure CDN 内容。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: allensu
ms.openlocfilehash: 7ae7224efdaa281106dfbe2118ab0092c8284c6e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260150"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>按国家/地区限制 Azure CDN 内容

## <a name="overview"></a>概述
当用户请求内容时，默认情况下，无论发出请求的用户位于哪里，系统都会提供该内容。 但是，在某些情况下，您可能希望按国家/地区限制对内容的访问。 使用*地理筛选*功能，您可以在 CDN 终结点上的特定路径上创建规则，以允许或阻止选定国家/地区中的内容。

> [!IMPORTANT]
> **Microsoft 的 Azure CDN 标准版**配置文件不支持基于路径的地区筛选。
> 

## <a name="standard-profiles"></a>标准版配置文件
本部分中的过程仅适用于**来自 Akamai 的标准 Azure CDN** 和**来自 Verizon 的标准 Azure CDN** 配置文件。 

对于**来自 Verizon 的高级 Azure CDN**配置文件，必须使用**管理**门户来激活地区筛选。 有关详细信息，请参阅[来自 Verizon 的高级 Azure CDN 配置文件](#azure-cdn-premium-from-verizon-profiles)。

### <a name="define-the-directory-path"></a>定义目录路径
若要访问地区筛选功能，请在门户中选择你的 CDN 终结点，然后在左侧菜单中的“设置”下选择“地区筛选”。**** 

![地区筛选标准](./media/cdn-filtering/cdn-geo-filtering-standard.png)

从“路径”框中，指定将允许或拒绝用户访问的位置的相对路径。**** 

可使用正斜杠 (/) 对所有文件应用地区筛选，也可通过指定目录路径（例如 */pictures/*）对选定文件夹应用该筛选。 也可以向单个文件应用地区筛选（例如 */pictures/city.png*）。 允许多个规则；输入一个规则后，会出现一个空白行供你用来输入下一个规则。

例如，以下所有目录路径筛选器都是有效的：   
*/*                                 
*/照片/*     
*/照片/斯特拉斯堡/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>定义操作的类型

从“操作”**** 列表中，选择 **“允许”** 或 **“阻止”**： 

- **允许**：只允许来自指定国家/地区的用户访问从递归路径请求的资产。

- **块**：来自指定国家/地区的用户被拒绝访问从递归路径请求的资产。 如果未为该位置配置其他国家/地区筛选选项，则将允许所有其他用户访问。

例如，用于阻止路径 */Photos/Strasbourg/* 的地区筛选规则将筛选以下文件：     
*\//http：\<端点>.azureedge.net/照片/斯特拉斯堡/1000.jpg*
*\//\<http： 端点>.azureedge.net/照片/斯特拉斯堡/大教堂/1000.jpg*

### <a name="define-the-countriesregions"></a>定义国家/地区
在 **"国家/地区代码"** 列表中，选择要阻止或允许路径的国家/地区。 

选择完国家/地区后，选择 **"保存"** 以激活新的地理筛选规则。 

![地区筛选规则](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>清理资源
若要删除某个规则，请从“地区筛选”**** 页面上的列表中选择它，然后选择“删除”****。

## <a name="azure-cdn-premium-from-verizon-profiles"></a>来自 Verizon 的高级 Azure CDN 配置文件
对于**来自 Verizon 的高级 Azure CDN** 配置文件，用于创建地区筛选规则的用户界面不同：

1. 从 Azure CDN 配置文件的顶部菜单中，选择“管理”****。

2. 从 Verizon 门户中，选择“HTTP 大型”****，然后选择“国家/地区筛选”****。

    ![地区筛选标准](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. 选择“添加国家/地区筛选器”****。

    此时将显示“步骤一：”**** 页面。

4. 输入目录路径，选择“阻止”或“添加”，然后选择“下一步”。************

    此时将显示“步骤二：”**** 页面。 

5. 从列表中选择一个或多个国家/地区，然后选择 **"完成"** 以激活规则。 
    
    新规则将显示在“国家/地区筛选”**** 页面上的表中。

    ![地区筛选规则](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>清理资源
在国家/地区筛选规则表中，选择规则旁边的删除图标以将其删除，或者选择编辑图标以对其进行修改。

## <a name="considerations"></a>注意事项
* 对地区筛选配置的更改不会立即生效：
   * 对于 **Microsoft 推出的 Azure CDN 标准版**配置文件，传播通常可在 10 分钟内完成。 
   * 对于 **Akamai 的 Azure CDN 标准版**配置文件，传播通常可在一分钟内完成。 
   * 对于“Verizon 提供的 Azure CDN 标准版”**** 和“Verizon 提供的 Azure CDN 高级版”**** 配置文件，传播通常在 10 分钟内完成。 
 
* 此功能不支持通配符（例如 *）。

* 与相对路径关联的地区筛选配置以递归方式应用于该路径。

* 只能对相同相对路径应用一个规则。 也就是说，您不能创建指向同一相对路径的多个国家/区域筛选器。 但是，由于国家/地区筛选器是递归的，因此文件夹可以有多个国家/区域筛选器。 换句话说，可以为以前配置的文件夹的子文件夹分配不同的国家/地区筛选器。

* 地理筛选功能使用国家/地区代码来定义允许或阻止安全目录请求的国家/地区。 虽然 Akamai 和 Verizon 配置文件支持的大多数国家/地区代码相同，但还是有一些差别。 有关详细信息，请参阅 [Azure CDN 国家/地区代码](/previous-versions/azure/mt761717(v=azure.100))。 

