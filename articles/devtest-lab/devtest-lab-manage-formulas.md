---
title: "管理 Azure 开发测试实验室中用于创建 VM 的公式 | Microsoft Docs"
description: "了解如何更新和删除 Azure 开发测试实验室公式"
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
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fd334dc8cd22990456888146f41389054f2e8b29
ms.lasthandoff: 03/15/2017


---
# <a name="manage-azure-devtest-labs-formulas"></a>管理 Azure 开发测试实验室公式

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

[创建 Azure 开发测试实验室公式](devtest-lab-create-formulas.md#create-a-formula)一文将指导你完成在 Azure 开发测试实验室中创建公式的过程。 创建公式后，本文将指导你完成管理公式。

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


