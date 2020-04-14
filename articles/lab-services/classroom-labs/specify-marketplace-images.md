---
title: 为 Azure 实验室服务中的实验室指定市场映像
description: 本文介绍如何指定实验室创建者可用于在 Azure 实验室服务中的实验室帐户中创建实验室的应用商店映像。
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
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: a64dee6da521764a38fc60bee06545f6a561c297
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257686"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>指定可供实验室创建者使用的市场映像
作为实验室帐户所有者，你可以指定可供实验室创建者用来在实验室帐户中创建实验室的市场映像。 

## <a name="select-images-available-for-labs"></a>选择可用于实验室的图像
在左侧的菜单上选择“市场映像”。**** 默认情况下，可以看到映像的完整列表（包括启用的和禁用的）。 您可以通过从顶部的下拉列表中选择/**"仅启用"/** 禁用的"**Enabled only**选项"来筛选列表，以仅查看已启用/禁用的图像。 
    
![“市场映像”页](../media/tutorial-setup-lab-account/marketplace-images-page.png)

列表中显示的市场映像只是满足以下条件的映像：
    
- 创建单个 VM。
- 使用 Azure 资源管理器预配 VM
- 不需要购买额外的许可计划

## <a name="disable-images-for-a-lab"></a>禁用实验室的图像 
要禁用实验室的单个图像，请选择 **...（省略）** 在最后一列中，并选择 **"禁用图像**"。 

![禁用一个映像](../media/tutorial-setup-lab-account/disable-one-image.png) 

或者，选择图像名称前的复选框，然后选择工具栏上的 **"禁用选定图像**"。 

要同时禁用多个图像，请在图像名称之前选择复选框，然后选择**工具栏上的"禁用所选图像**"。 

![禁用多个映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>为实验室启用图像
要启用禁用的图像，请选择 **...（省略）** 在最后一列中，选择 **"启用图像**"。 或者，选择图像名称前的复选框，然后选择工具栏上的 **"启用选定的图像**"。 

要同时禁用多个图像，请在图像名称之前选择复选框，然后选择工具栏上的 **"启用选定图像**"。 

## <a name="enable-images-at-the-time-of-lab-creation"></a>在实验室创建时启用图像
您可以在实验室创建时启用更多图像： 

1. 使用**实验室帐户所有者**凭据登录到[Azure 实验室服务网站](https://labs.azure.com)
2. 选择默认虚拟机映像或向下箭头。 
3. 选择 **"启用更多图像"选项**。 

    ![启用更多图像选项](../media/specify-marketplace-images/enable-more-images-menu.png)
4. 按照上一节的说明启用您选择的图像。 
5. 您可能需要关闭 **"新建"实验室**窗口并重新打开它才能查看在上一步骤中选择的图像。 



## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
