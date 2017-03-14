---
title: "管理 Azure 开发测试实验室中用于创建 VM 的公式 | Microsoft Docs"
description: "了解如何创建、更新和删除 Azure 开发测试实验室公式并用其创建新的 VM。"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 52590f0c47954d115f39cbe988e04a0ed3feb94d
ms.openlocfilehash: cd3352ae30734cce116fda5a838f95e5e0b86a8a
ms.lasthandoff: 02/27/2017


---
# <a name="manage-devtest-labs-formulas-to-create-vms"></a>管理用于创建 VM 的开发测试实验室公式
Azure 开发测试实验室中的公式是用于创建虚拟机（VM）的默认属性值的列表。 从公式创建 VM 时，可以按原样使用默认值，也可以修改默认值。 如[自定义映像](devtest-lab-create-template.md)和[应用商店映像](devtest-lab-configure-marketplace-images.md)，公式提供了进行快速 VM 预配的机制。  

在本文中，将学习如何执行以下任务：

* [创建公式](#create-a-formula)
* [使用公式配置 VM](#use-a-formula-to-provision-a-vm)
* [公式](#modify-a-formula)
* [删除公式](#delete-a-formula)

> [!NOTE]
> 公式（如[自定义映像](devtest-lab-create-template.md)）允许从 VHD 文件创建基本映像。 然后可以使用此基本映像设置新的 VM。 为了帮助确定哪种适合你的特定环境，请参阅文章[在开发测试实验室中比较自定义映像和公式](devtest-lab-comparing-vm-base-image-types.md)。
> 
> 

## <a name="create-a-formula"></a>创建公式
任何拥有开发测试实验室用户权限的用户都可以使用公式作为基础创建 VM。 有两种创建公式的方法： 

* 从基项 - 希望定义公式的所有特征时使用。
* 从现有实验室 VM - 希望根据现有 VM 的设置创建公式时使用。

### <a name="create-a-formula-from-a-base"></a>从基项创建公式
以下步骤介绍从自定义映像、应用商店映像或其他公式创建公式的过程。

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表，选择所需实验室。  
4. 在实验室的边栏选项卡上，选择“公式（可重用基项）”。
   
    ![公式菜单](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在“实验室公式”边栏选项卡上，选择“+ 添加”。
   
    ![添加公式](./media/devtest-lab-manage-formulas/add-formula.png)
6. 在“选择基项”边栏选项卡上，选择要从中创建公式的基项（自定义映像、应用商店映像或公式）。
   
    ![基项列表](./media/devtest-lab-manage-formulas/base-list.png)
7. 在“创建公式”边栏选项卡上，指定以下值：
   
   * “公式名称” - 输入公式的名称。 创建 VM 时，此值将显示在基本映像列表中。 该名称在键入时进行验证，如果无效，则会显示一条消息，指出有效名称的要求。
   * “说明” - 为公式输入有意义的说明。 创建 VM 时，可以从公式上下文菜单中获取此值。
   * “用户名” - 输入将被授予管理员权限的用户名。
   * “密码” - 输入或从下拉列表中选择与要用于指定用户的密码相关联的值。  
   * “映像” - 此字段显示在上一个边栏选项卡上选择的基本映像的名称。 
   * “虚拟机大小” - 选择指定要创建的 VM 的处理器内核、RAM 大小和硬盘驱动器大小的预定义项之一。
   * “虚拟网络” - 指定所需的虚拟网络。
   * “子网” - 指定所需的子网。
   * “公共 IP 地址” - 如果实验室策略设置为允许所选子网的公共 IP 地址，请通过选择“是”或“否”来指定是否要公开此IP地址。 否则，将禁用此选项并选择为“否”。
   * “项目” - 选择并配置要添加到基本映像的项目。 安全字符串值不随公式一起保存。 因此，不显示作为安全字符串的项目参数。 
     
       ![创建公式](./media/devtest-lab-manage-formulas/create-formula.png)
8. 选择“创建”来创建公式。

### <a name="create-a-formula-from-a-vm"></a>从 VM 创建公式
以下步骤介绍基于现有 VM 创建公式的过程。 

> [!NOTE]
> 若要从 VM 创建公式，必须在 2016 年 3 月 30 日之后创建 VM。 
> 
> 

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表，选择所需实验室。  
4. 在实验室的“概述”边栏选项卡上，选择要从中创建公式的 VM。
   
    ![实验室 VM](./media/devtest-lab-manage-formulas/my-vms.png)
5. 在 VM 的边栏选项卡，选择“创建公式（可重用基项）”。
   
    ![创建公式](./media/devtest-lab-manage-formulas/create-formula-menu.png)
6. 在“创建公式”边栏选项卡，输入新公式的“名称”和“说明”。
   
    ![创建公式边栏选项卡](./media/devtest-lab-manage-formulas/create-formula-blade.png)
7. 选择“确定”创建公式。

## <a name="use-a-formula-to-provision-a-vm"></a>使用公式配置 VM
公式创建完成后，便可创建基于该公式的 VM。 [添加包含项目的 VM](devtest-lab-add-vm-with-artifacts.md) 一文指导你完成该过程。

## <a name="modify-a-formula"></a>修改公式
若要修改公式，请按照下列步骤操作：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表，选择所需实验室。  
4. 在实验室的边栏选项卡上，选择“公式（可重用基项）”。
   
    ![公式菜单](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在“实验室公式”边栏选项卡上，选择要修改的公式。
6. 在“更新公式”边栏选项卡上，进行所需的编辑，然后选择“更新”。

## <a name="delete-a-formula"></a>删除公式
若要删除公式，请按照下列步骤操作：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表，选择所需实验室。  
4. 在实验室“设置”边栏选项卡上，选择“公式”。
   
    ![公式菜单](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在“实验室公式”边栏选项卡上，选择要删除的公式右侧的省略号。
   
    ![公式菜单](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 在公式的上下文菜单上，选择“删除”。
   
    ![公式上下文菜单](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 在删除确认对话框中，选择“是”。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章
* [自定义映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>后续步骤
创建完用于创建 VM 的公式后，下一步就是[将 VM 添加到实验室](devtest-lab-add-vm-with-artifacts.md)。


