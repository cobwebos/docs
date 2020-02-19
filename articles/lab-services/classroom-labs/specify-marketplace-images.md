---
title: 在 Azure 实验室服务中指定实验室的 marketplace 映像
description: 本文介绍如何指定实验室创建者可用于在 Azure 实验室服务中的实验室帐户中创建实验室的 Marketplace 映像。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444662"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>指定可供实验室创建者使用的市场映像
作为实验室帐户所有者，你可以指定可供实验室创建者用来在实验室帐户中创建实验室的市场映像。 

## <a name="select-images-available-for-labs"></a>选择可用于实验室的映像
在左侧的菜单上选择“市场映像”。 默认情况下，可以看到映像的完整列表（包括启用的和禁用的）。 可以通过从顶部的下拉列表中选择“仅启用的**仅禁用的”选项对列表进行筛选来仅查看启用的/禁用的映像。** / 
    
![“市场映像”页](../media/tutorial-setup-lab-account/marketplace-images-page.png)

列表中显示的市场映像只是满足以下条件的映像：
    
- 创建单个 VM。
- 使用 Azure 资源管理器预配 VM
- 不需要购买额外的许可计划

## <a name="disable-images-for-a-lab"></a>为实验室禁用映像 
若要为实验室禁用单个映像，请选择 " **..."（省略号）** ，然后选择 "**禁用映像**"。 

![禁用一个映像](../media/tutorial-setup-lab-account/disable-one-image.png) 

另外，还可以选择映像名称前面的复选框，然后选择工具栏上的 "**禁用选定的图像**"。 

若要同时禁用多个映像，请在映像名称之前选择 checkbox，然后选择工具栏上的 "**禁用所选图像**"。 

![禁用多个映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>为实验室启用映像
若要启用已禁用的映像，请选择 " **..."（省略号）** ，然后选择 "**启用映像**"。 另外，还可以选择映像名称前面的复选框，然后选择工具栏上的 "**启用选定的图像**"。 

若要同时禁用多个映像，请在映像名称之前选择 checkbox，然后选择工具栏上的 "**启用选定的图像**"。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
