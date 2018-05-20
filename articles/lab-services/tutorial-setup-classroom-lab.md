---
title: 使用 Azure 实验室服务设置课堂实验室 | Microsoft Docs
description: 在此教程中，将设置要在课堂中使用的实验室。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 3e9f8d118c4413ec93b7dffcfa41b6ad4381b052
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-classroom-lab"></a>教程：设置课堂实验室 
在此教程中，将设置带有一组虚拟机的课堂实验室，以供学生们在课堂上使用。  

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建课堂实验室
> * 配置课堂实验室
> * 将注册链接发送给学生

## <a name="create-a-classroom-lab"></a>创建课堂实验室

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。
2. 在“新建实验室”窗口中，执行以下操作：： 
    1. 指定课堂实验室的“名称”。 
    2. 选择计划在课堂中使用的虚拟机的“大小”。
    3. 选择用于创建虚拟机的“映像”。
    4. 指定用于登录到实验室中的虚拟机的“默认凭据”。 
    7. 选择“保存”。

        ![创建课堂实验室](./media/tutorial-setup-classroom-lab/new-lab-window.png)
1. 可以查看实验室的“主页”。 
    
    ![课堂实验室主页](./media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>配置使用策略

1. 选择“使用策略”。 
2. 在“使用策略”设置中，输入允许使用实验室的“用户数量”。
3. 选择“保存”。 

    ![使用策略](./media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>设置模板 
实验室中的模板用于创建所有用户的虚拟机。 设置模板虚拟机，为其准确配置需要提供给实验室用户的内容。 可以提供显示给实验室用户的模板名称和说明，并将可见性设置为“公共”，让实验室用户能够使用模板 VM 的实例。 

### <a name="set-title-and-description"></a>设置标题和说明
1. 在“模板”部分，选择与模板相对应的“编辑”（铅笔图标）。 
2. 在“用户视图”窗口中，输入模板的“标题”。
3. 输入模板的“说明”。
4. 选择“保存”。

    ![课堂实验室说明](./media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>公开模板实例
将模板的可见性设置为“公共”后，Azure 实验室服务就会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数就是允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。 

1. 在“模板”部分选择“可见性”。 
2. 在“可用性”页中选择“公共”。
    
    > [!IMPORTANT]
    > 将模板设置为公用以后，便不能将其访问权限更改为专用。 
3. 选择“保存”。

    ![可用性](./media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>将注册链接发送给学生

1. 选择“用户注册”磁贴。
2. 在“用户注册”对话框中，选择“复制”按钮。 将链接复制到剪贴板。 将其粘贴到电子邮件编辑器，然后向学生发送电子邮件。 

    ![学生注册链接](./media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>后续步骤
在本教程中，创建并配置了课堂实验室。 若要了解学生如何使用注册链接访问实验室中的 VM，请转到下一教程：

> [!div class="nextstepaction"]
> [连接到课堂实验室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)

