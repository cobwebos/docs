---
title: 管理 Azure 开发测试实验室中用于创建 VM 的公式 | Microsoft Docs
description: 了解如何更新和删除 Azure 开发测试实验室公式
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: d8f2ae64e2f8e694de5a7cf5aa9049e63998dca0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60562466"
---
# <a name="manage-azure-devtest-labs-formulas"></a>管理 Azure 开发测试实验室公式

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

本文介绍如何从基项（自定义映像、市场映像或其他公式）或现有 VM 创建公式。 本文还引导完成管理现有公式的操作。

## <a name="create-a-formula"></a>创建公式
任何拥有开发测试实验室用户  权限的用户都可以使用公式作为基础创建 VM。 有两种创建公式的方法： 

* 从基项 - 希望定义公式的所有特征时使用。
* 从现有实验室 VM - 希望根据现有 VM 的设置创建公式时使用。

有关添加用户和权限的详细信息，请参阅[在 Azure 开发测试实验室中添加所有者和用户](./devtest-lab-add-devtest-user.md)。

### <a name="create-a-formula-from-a-base"></a>从基项创建公式
以下步骤介绍从自定义映像、市场映像或其他公式创建公式的过程。

1. 登录到 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

2. 选择“所有服务”  ，并从列表中选择“开发测试实验室”  。

3. 从实验室列表，选择所需的实验室。  

4. 在实验室的页面上，选择“公式（可重用基项）”  。
   
    ![公式菜单](./media/devtest-lab-create-formulas/lab-settings-formulas.png)

5. 在“公式”  页面上，选择“+ 添加”  。
   
    ![添加公式](./media/devtest-lab-create-formulas/add-formula.png)

6. 在“选择基项”  页面上，选择要从中创建公式的基项（自定义映像、市场映像或公式）。
   
    ![基项列表](./media/devtest-lab-create-formulas/base-list.png)

7. 在“创建公式”  页面的“基本设置”  选项卡上，指定以下值：
   
    * “公式名称”  - 输入公式的名称。 创建 VM 时，此值会显示在基本映像列表中。 在键入该名称时会对其进行验证，如果无效，则会显示一条消息，指出有效名称的要求。
    * “用户名”  - 输入被授予管理员权限的用户名。
    * “密码”  - 输入或从下拉列表中选择与要用于指定用户的密码相关联的值。 若要了解如何在密钥保管库中保存机密并在创建实验室资源时使用这些机密，请参阅[在 Azure 密钥保管库中存储机密](devtest-lab-store-secrets-in-key-vault.md)。
    * **VM 大小** - 选择“更改大小”  以更改 VM 的大小。 
    * **项目** - 选择“添加或删除项目”  页面，可以从中选择并配置要添加到基本映像的项目。 有关项目的详细信息，请参阅[创建 Azure 开发测试实验室虚拟机的自定义项目](devtest-lab-artifact-author.md)。
8. 切换到“高级设置”  选项卡，并指定以下值：
    - **虚拟网络** - 若要更改虚拟网络，请选择“更改 VNet”  。 
    - **子网** - 若要更改子网，请选择“更改子网”  。 
    - “IP 地址配置”  - 指定希望使用公共、私有还是共享 IP 地址。 有关共享 IP 地址的详细信息，请参阅[了解 Azure 开发测试实验室中的共享 IP 地址](./devtest-lab-shared-ip.md)。
    - **到期日期和时间** - 指定 VM 的到期日期和时间，以便自动删除 VM。 
    - “使此计算机可索取”  - 使计算机可“索取”的意思是在创建时不会为其分配所有权。 而是，实验室用户将能够在实验室的页面上取得（“索取”）计算机的所有权。     
    - **可认领的实例数**-指定想要创建可认领的实例的数量。 
8. 选择“提交”  以创建公式。

9. 创建公式后，它会显示在“公式”  页面上的列表中。

### <a name="create-a-formula-from-a-vm"></a>从 VM 创建公式
以下步骤介绍基于现有 VM 创建公式的过程。 

> [!NOTE]
> 若要从 VM 创建公式，必须在 2016 年 3 月 30 日之后创建 VM。 
> 
> 

1. 登录到 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“所有服务”  ，并从列表中选择“开发测试实验室”  。
3. 从实验室列表，选择所需的实验室。  
4. 在实验室的“概述”  页面上，选择要从中创建公式的 VM。
   
    ![实验室 VM](./media/devtest-lab-create-formulas/my-vms.png)
5. 在 VM 的页面上，选择“创建公式（可重用基项）”  。
   
    ![创建公式](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. 在“创建公式”  页面上，输入新公式的“名称”  和“说明”  。
   
    ![“创建公式”页面](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. 选择“确定”  创建公式。

## <a name="modify-a-formula"></a>修改公式
若要修改公式，请按照下列步骤操作：

1. 登录到 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“所有服务”  ，并从列表中选择“开发测试实验室”  。
3. 从实验室列表，选择所需的实验室。  
4. 在实验室的页面上，选择“公式（可重用基项）”  。
   
    ![公式菜单](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在“实验室公式”  页面上，选择要修改的公式。
6. 在“更新公式”  页面上，进行所需的编辑，并选择“更新”  。

## <a name="delete-a-formula"></a>删除公式
若要删除公式，请按照下列步骤操作：

1. 登录到 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“所有服务”  ，并从列表中选择“开发测试实验室”  。
3. 从实验室列表，选择所需的实验室。  
4. 在实验室“设置”  页面上，选择“公式”  。
   
    ![公式菜单](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. 在“实验室公式”  页面上，选择要删除的公式右侧的省略号。
   
    ![公式菜单](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. 在公式的上下文菜单上，选择“删除”  。
   
    ![公式上下文菜单](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. 在删除确认对话框中，选择“是”  。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章
* [自定义映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>后续步骤
创建完用于创建 VM 的公式后，下一步就是[将 VM 添加到实验室](devtest-lab-add-vm.md)。

