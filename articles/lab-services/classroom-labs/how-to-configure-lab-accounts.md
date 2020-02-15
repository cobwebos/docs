---
title: 在 Azure 实验室服务中配置实验室帐户 |Microsoft Docs
description: 本文介绍如何创建实验室帐户、查看所有实验室帐户或在 Azure 实验室服务中删除实验室帐户。
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 37a657093fd55ce752095417fe744f83946962db
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210563"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>在 Azure 实验室服务中配置实验室帐户 
在 Azure 实验室服务中，实验室帐户是托管实验室类型的容器，例如教室实验室。 管理员可以设置一个具有 Azure 实验室服务的实验室帐户，并为能够在帐户中创建实验室的实验室所有者提供访问权限。 本文介绍如何创建实验室帐户、查看所有实验室帐户，或者删除实验室帐户。

## <a name="connect-with-a-peer-virtual-network"></a>与对等虚拟网络连接
若要将虚拟网络作为对等网络连接到实验室的虚拟网络，请执行以下步骤：

1. 在 "**实验室帐户**" 页上，选择左侧菜单上的 "**实验室配置**"。

    ![实验室配置页](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. 对于**对等虚拟网络**，选择 "**已启用**" 或 "**已禁用**"。 默认值为 "**已禁用**"。 若要启用对等虚拟网络，请执行以下步骤： 
    1. 选择“启用”。
    2. 从下拉列表中选择**VNet** 。 
3. 在工具栏上选择“保存”。 

此帐户中创建的实验室已连接到所选虚拟网络。 它们可以访问所选虚拟网络中的资源。 有关详细信息，请参阅[在 Azure 实验室服务中将实验室的网络与对等虚拟网络连接](how-to-connect-peer-virtual-network.md)。

为**对等虚拟网络**字段选择虚拟网络后，将禁用 "**允许实验室创建者选取实验室位置**" 选项。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域，才能与对等虚拟网络中的资源进行连接。 

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>在实验室中为 Vm 指定地址范围
以下过程包含在实验室中为 Vm 指定地址范围的步骤。 如果更新之前指定的范围，则修改后的地址范围仅适用于在进行更改后创建的 Vm。 

在指定应记住的地址范围时，有一些限制。 

- 前缀必须小于或等于23。 
- 如果虚拟网络对等互连到实验室帐户，则提供的地址范围不能与对等互连虚拟网络中的地址范围重叠。

1. 在 "**实验室帐户**" 页上，选择左侧菜单上的 "**实验室配置**"。
2. 为 "**地址范围**" 字段指定将在实验室中创建的 vm 的地址范围。 地址范围应为无类域间路由（CIDR）表示法（例如： 10.20.0.0/23）。 将在此地址范围内创建实验室中的虚拟机。
3. 在工具栏上选择“保存”。 

    ![配置地址范围](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>将用户添加为“实验室创建者”角色
若要在实验室帐户中设置课堂实验室，用户必须是实验室帐户中“实验室创建者”角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 如果打算使用同一用户帐户创建课堂实验室，可以跳过此步骤。 若要使用其他用户帐户创建教室实验室，请执行以下步骤： 

为教师提供为课程创建实验室的权限，并将他们添加为**实验室创建者**角色：

1. 在“实验室帐户”页上，选择“访问控制(IAM)”，然后单击工具栏中的“+ 添加角色分配”。 

    ![“访问控制”->“添加角色分配”按钮](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在“添加角色分配”页上，选择“实验室创建者”作为“角色”，选择想要添加到“实验室创建者”角色的用户，然后选择“保存”。 

    ![添加实验室创建者](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > 如果要添加非 Microsoft 帐户用户作为实验室创建者，请参阅[将非 Microsoft 帐户用户添加为实验室创建者](#add-a-non-microsoft-account-user-as-a-lab-creator)部分。 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>指定可供实验室创建者使用的市场映像
作为实验室帐户所有者，你可以指定可供实验室创建者用来在实验室帐户中创建实验室的市场映像。 

1. 在左侧的菜单上选择“市场映像”。 默认情况下，可以看到映像的完整列表（包括启用的和禁用的）。 可以通过从顶部的下拉列表中选择“仅启用的**仅禁用的”选项对列表进行筛选来仅查看启用的/禁用的映像。** / 
    
    ![“市场映像”页](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    列表中显示的市场映像只是满足以下条件的映像：
        
    - 创建单个 VM。
    - 使用 Azure 资源管理器预配 VM
    - 不需要购买额外的许可计划
2. 若要**禁用**已启用的市场映像，请执行下列操作之一： 
    1. 选择最后一列中的“...”（省略号）并选择“禁用映像”。 

        ![禁用一个映像](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 通过选中列表中映像名称前面的复选框从列表中选择一个或多个映像，然后选择“禁用所选映像”。 

        ![禁用多个映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 类似地，若要**启用**市场映像，请执行下列操作之一： 
    1. 选择最后一列中的“...”（省略号）并选择“启用映像”。 
    2. 通过选中列表中映像名称前面的复选框从列表中选择一个或多个映像，然后选择“启用所选映像”。 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>添加非 Microsoft 帐户用户作为实验室创建者
若要将用户添加为实验室创建者，请使用其电子邮件帐户。 可以使用以下类型的电子邮件帐户：

- 大学办公室 365 Azure Active Directory （AAD）提供的电子邮件帐户。 
- Microsoft 电子邮件帐户，如 `@outlook.com`、`@hotmail.com`、`@msn.com`或 `@live.com`。
- 非 Microsoft 电子邮件帐户，如 Yahoo 或 Google 提供的帐户。 但是，这些类型的帐户必须与 Microsoft 帐户链接。
- 一个 GitHub 帐户。 此帐户必须与 Microsoft 帐户链接。

### <a name="using-a-non-microsoft-email-account"></a>使用非 Microsoft 电子邮件帐户
实验室创建者/讲师可以使用非 Microsoft 电子邮件帐户来注册和登录到教室实验室。  但是，登录到实验室服务门户要求讲师首先创建一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。

许多教师可能已经有一个链接到非 Microsoft 电子邮件地址的 Microsoft 帐户。 例如，如果讲师已经将其电子邮件地址与 Microsoft 的其他产品或服务（如 Office、Skype、OneDrive 或 Windows）一起使用，则该 Microsoft 帐户。  

当教师登录到实验室服务门户时，会提示他们输入其电子邮件地址和密码。 如果讲师尝试使用未链接 Microsoft 帐户的非 Microsoft 帐户进行登录，则讲师将收到以下错误消息： 

![错误消息](../media/how-to-configure-student-usage/cant-find-account.png)

若要注册 Microsoft 帐户，讲师应使用[http://signup.live.com](http://signup.live.com)。  


### <a name="using-a-github-account"></a>使用 GitHub 帐户
讲师还可以使用现有的 GitHub 帐户注册和登录到教室实验室。 如果讲师已经有一个链接到其 GitHub 帐户的 Microsoft 帐户，则他们可以登录并提供其密码，如前一部分中所示。 如果尚未将其 GitHub 帐户链接到 Microsoft 帐户，则应选择 "**登录" 选项**：

![登录选项链接](../media/how-to-configure-student-usage/signin-options.png)

在 "**登录选项**" 页面上，选择 "**用 GitHub 登录**"。

![用 GitHub 链接登录](../media/how-to-configure-student-usage/signin-github.png)

最后，系统将提示他们创建链接到其 GitHub 帐户的 Microsoft 帐户。 当讲师选择**下一个**时，会自动发生这种情况。  然后，教师会立即登录并连接到教室实验室。

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>断开连接时自动关闭 Vm
断开远程桌面连接后，可以启用或禁用 Windows 实验室 Vm （模板或学生）的自动关闭。 你还可以指定 Vm 在自动关机之前应等待用户重新连接的时间。

![实验室帐户中的自动关闭设置](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

此设置适用于在实验室帐户中创建的所有实验室。 实验室所有者可以在实验室级别覆盖此设置。 在实验室帐户中对此设置的更改将只影响在更改后创建的实验室。

若要了解实验室所有者如何在实验室级别配置此设置，请参阅[此文](how-to-enable-shutdown-disconnect.md)

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)
