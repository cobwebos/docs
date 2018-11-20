---
title: 在 Azure 实验室服务中管理课堂实验室的模板 | Microsoft Docs
description: 了解如何在 Azure 实验室服务中创建和管理课堂实验室模板。
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
ms.date: 10/25/2018
ms.author: spelluru
ms.openlocfilehash: 3ecbef3b3063ceb413b852f8000b44a85d28d08e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142673"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>在 Azure 实验室服务中创建和管理课堂模板
实验室中的模板是基础虚拟机映像，用户的所有虚拟机均依据其创建。 设置模板虚拟机，为其准确配置需要提供给实验室用户的内容。 你可以提供实验室用户看到的模板的名称和描述。 然后发布此模板，让模板 VM 的实例可供实验室用户使用。 发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。

本文介绍如何在 Azure 实验室服务的课堂实验室中创建和管理模板虚拟机。 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>创建课堂实验室时发布模板
首先，可以在创建课堂实验室时设置并发布模板。

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 
2. 选择“登录”并输入凭据。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 在“新建实验室”窗口中，执行以下操作：： 
    1. 指定实验室的**名称**。 
    2. 指定允许加入实验室的最大**用户数**。 
    6. 选择“保存”。

        ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. 在“选择虚拟机规范”页上执行以下步骤：
    1. 选择在实验室中创建的虚拟机 (VM) 的**大小**。 
    2. 选择要在其中创建 VM 的**区域**。 
    3. 选择用于在实验室中创建 VM 的 **VM 映像**。 
    4. 选择“**下一步**”。

        ![指定 VM 规范](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. 在“设置凭据”页上，指定实验室中所有 VM 的默认凭据。 
    1. 指定实验室中所有 VM 的**用户名**。
    2. 指定用户的**密码**。 

        > [!IMPORTANT]
        > 记下用户名和密码。 这些信息不会再次显示。
    3. 选择“创建”。 

        ![设置凭据](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. 在“配置模板”页上，查看实验室创建过程的状态。 在实验室中创建模板最长需要 20 分钟时间。 

    ![配置模板](../media/tutorial-setup-classroom-lab/configure-template.png)
7. 配置完模板后，会看到以下页： 

    ![完成配置后的“配置模板”页](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. 在本教程中，以下步骤是可选步骤： 
    1. 选择“启动”来启动模板 VM。
    2. 选择“连接”以连接到模板 VM。 
    3. 在模板 VM 上安装并配置软件。 
    4. **停止** VM。  
    5. 输入模板的**说明**

        ![“配置模板”页上的“下一步”](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. 在模板页上选择“下一步”。 
10. 在“发布模板”页上执行以下操作。 
    1. 若要立即发布模板，请选中“我了解发布后无法修改模板。此过程只能执行一次，最长可能需要一小时”对应的复选框，然后选择“发布”。  

        > [!WARNING]
        > 发布后无法取消发布。 
    2. 若要在以后发布，请选择“保存供以后发布”。 可以在完成向导后发布模板 VM。 有关如何在完成向导后进行配置和发布的详细信息，请参阅[如何管理课堂实验室](how-to-manage-classroom-labs.md)一文中的[发布模板](#publish-the-template)部分。

        ![发布模板](../media/tutorial-setup-classroom-lab/publish-template.png)
11. 可以查看**发布模板的进度**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. 成功发布模板后，会看到以下页。 选择“完成”。

    ![发布模板 - 成功](../media/tutorial-setup-classroom-lab/publish-success.png)
1. 实验室的**仪表板**随即显示。 
    
    ![课堂实验室仪表板](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="set-up-a-template-after-creating-a-lab"></a>创建实验室后设置模板 
还可以在创建实验室后设置模板。   

### <a name="set-template-title-and-description"></a>设置模板标题和说明
1. 在“模板”部分，选择与模板相对应的“编辑”（铅笔图标）。 
2. 在“用户视图”窗口中，输入模板的“标题”。
3. 输入模板的“说明”。
4. 选择“保存”。

    ![课堂实验室说明](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>设置模板 VM
 请先连接到模板 VM 并在其上安装任何必需的软件，然后再将其提供给学生。 

1. 等待模板虚拟机准备就绪。 在它准备就绪后，“启动”按钮会变为已启用状态。 若要启动此 VM，请选择“启动”。

    ![启动模板 VM](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. 若要连接到 VM，请选择“连接”，然后按说明操作。 

    ![连接到模板 VM](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. 安装学生进行实验室操作所需的任何软件（例如，Visual Studio、Azure 存储资源管理器，等等）。 
2. 断开与模板 VM 的连接（关闭远程桌面会话）。 
3. 选择“停止”以**停止**模板 VM。 

    ![停止模板 VM](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


## <a name="publish-the-template"></a>发布模板 
如果不在创建实验室时发布模板，可在以后再发布。 在发布之前，可能需要连接到模板 VM，并使用任何软件对其进行更新。 发布模板时，Azure 实验室服务会使用该模板在实验室中创建 VM。 在此过程中创建的 VM 数等于允许进入实验室的最大用户数，这可以在实验室的使用策略中设置。 所有虚拟机的配置都与模板相同。 

1. 在“模板”部分中选择“发布”。 

    ![发布模板 VM](../media/tutorial-setup-classroom-lab/public-access.png)
1. 在“发布模板”消息框中查看消息，然后选择“发布”。 此过程可能需要花费一段时间，具体取决于要创建的 VM 数，该数目也是允许进入实验室的用户数。
    
    > [!IMPORTANT]
    > 模板一旦发布，便不能取消发布。 不过，可以重新发布模板。 
4. 切换到“虚拟机”页，确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 

    ![虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. 等待 VM 创建完毕。 它们应该处于“已停止”状态。 可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>重新发布模板 
发布模板后，仍可以连接到 VM 的模板、对其进行更新，然后将其重新发布。 重新发布模板 VM 时，所有用户 VM 将断开连接，然后根据更新的模板重新创建这些 VM。 

1. 在实验室的仪表板页上，选择“模板”部分的“重新发布”。 

    ![“重新发布”按钮](../media/how-to-create-manage-template/republish-button.png)
2. 在“重新发布模板”消息框中查看文本，然后选择“重新发布”以继续。 否则请选择“取消”。 

    ![“重新发布模板”消息](../media/how-to-create-manage-template/republish-template-message.png)
3. 在“模板”部分的磁贴上可以看到重新发布的状态。

    ![重新发布的状态](../media/how-to-create-manage-template/republish-status-publishing.png)
4. 发布模板后，状态将设置为“已发布”。 

    ![重新发布成功](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>后续步骤
开始使用 Azure 实验室服务设置实验室：

- [设置课堂实验室](how-to-manage-classroom-labs.md)
- [设置实验室](../tutorial-create-custom-lab.md)
