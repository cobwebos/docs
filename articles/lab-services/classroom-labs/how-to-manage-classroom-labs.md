---
title: 管理 Azure 实验室服务中的教室实验室 | Microsoft Docs
description: 了解如何创建和配置教室实验室、如何查看所有教室实验室、如何与实验室用户共享注册链接，或者如何删除实验室。
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 705209becff7c8ad20e7d09f056aa1ae1577b7ae
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658972"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>管理 Azure 实验室服务中的教室实验室 
本文介绍如何创建和配置教室实验室、如何查看所有教室实验室，或者如何删除教室实验室。

## <a name="create-a-classroom-lab"></a>创建教室实验室

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。
2. 在“新建实验室”窗口中，执行以下操作：： 
    1. 指定课堂实验室的“名称”。 
    2. 选择计划在课堂中使用的虚拟机的“大小”。
    3. 选择用于创建虚拟机的**映像**。
    4. 指定用于登录到实验室中的虚拟机的**默认凭据**。
    7. 选择“保存”。

        ![创建课堂实验室](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. 可以查看实验室的“主页”。 
    
    ![课堂实验室主页](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>配置使用策略

1. 选择“使用策略”。 
2. 在“使用策略”设置中，输入允许使用实验室的“用户数量”。
3. 选择“保存”。 

    ![使用策略](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>设置模板
实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置模板虚拟机，为其准确配置需要提供给实验室用户的内容。 你可以提供实验室用户看到的模板的名称和描述。 将模板的可见性设置为“公共”，让模板 VM 的实例可供实验室用户使用。  

### <a name="set-template-title-and-description"></a>设置模板标题和说明
1. 在“模板”部分，选择与模板相对应的“编辑”（铅笔图标）。 
2. 在“用户视图”窗口中，输入模板的“标题”。
3. 输入模板的“说明”。
4. 选择“保存”。

    ![课堂实验室说明](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>公开模板实例 
将模板的可见性设置为“公共”后，Azure 实验室服务就会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。  

1. 在“模板”部分选择“可见性”。 
2. 在“可用性”页中选择“公共”。
    
    > [!IMPORTANT]
    > 将模板设置为公用以后，便不能将其访问权限更改为专用。 
3. 选择“保存”。

    ![可用性](../media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>将注册链接发送给学生

1. 选择“用户注册”磁贴。
2. 在“用户注册”对话框中，选择“复制”按钮。 将链接复制到剪贴板。 将其粘贴到电子邮件编辑器，然后向学生发送电子邮件。 

    ![学生注册链接](../media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>查看所有教室实验室
1. 导航到 [Azure 实验室服务门户](https://labs.azure.com)。
2. 确认在所选实验室帐户中看到所有实验室。 

    ![所有实验室](../media/how-to-manage-classroom-labs/all-labs.png)
3. 使用顶部的下拉列表选择另一实验室帐户。 此时会在所选实验室帐户中看到实验室。 

## <a name="delete-a-classroom-lab"></a>删除教室实验室
1. 在实验室对应的磁贴中，选择角落里的三个点 (...)。 

    ![选择实验室](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. 选择“删除”。 

    ![“删除”按钮](../media/how-to-manage-classroom-labs/delete-button.png)
3. 在“删除实验室”对话框中，选择“删除”。 

    ![“删除”对话框](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>后续步骤
开始使用 Azure 实验室服务设置实验室：

- [设置课堂实验室](how-to-manage-classroom-labs.md)
- [设置实验室](../tutorial-create-custom-lab.md)
