---
title: "管理 Azure 开发测试实验室自定义映像以创建 VM | Microsoft Docs"
description: "了解如何从 VHD 文件或从 Azure DevTest 实验室中的现有虚拟机创建自定义映像"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 62d64f5a59a003b2ca15d424c8844dee716bf147


---
# <a name="manage-azure-devtest-labs-custom-images-to-create-vms"></a>管理 Azure DevTest 实验室自定义映像以创建虚拟机
在 Azure DevTest 实验室中，自定义映像可快速创建虚拟机，无需让所有所需的软件都安装在目标计算机上。 自定义映像，可以在 VHD 文件中预安装所需的所有软件，然后使用 VHD 文件创建虚 VM。 已安装该软件，因为 VM 创建速度更快。 此外，通过从虚拟机创建自定义映像，将其用于克隆虚拟机，然后从该自定义映像创建虚拟机。

在本文中，学习如何：

* [从 VHD 文件创建自定义图像](#create-a-custom-image-from-a-vhd-file)，以便可以从该自定义映像创建虚拟机。 
* [从虚拟机创建自定义映像](#create-a-custom-image-from-a-vm)快速 VM 克隆。

## <a name="create-a-custom-image-from-a-vhd-file"></a>从 VHD 文件创建自定义映像
在此部分中，将看到如何从 VHD 文件创建自定义映像。
需要访问有效的 VHD 文件，以便执行本节中的所有步骤。   

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表中，选择所需的实验室。  
4. 在实验室的边栏选项卡，选择“配置”。 
5. 在实验室“配置” 边栏选项卡上，选择“自定义映像”。
6. 在“自定义映像” 边栏选项卡上，选择“+ 自定义图像”。
   
    ![添加自定义映像](./media/devtest-lab-create-template/add-custom-image.png)
7. 输入自定义映像的名称。 在创建 VM 时，此名称显示在基础映像的列表中。
8. 键入自定义映像的说明。 说明显示在基础映像的列表中。
9. 选择 **VHD**。
10. 如果未列出具有访问权限的 VHD 文件，按照 [VHD 文件](#upload-a-vhd-file) 部分的说明，添加具有访问权限的 VHD 文件，然后在完成后返回此处。
11. 选择所需的 VHD 文件。
12. 选择“确定”关闭“VHD 文件”边栏选项卡。
13. 选择“OS 配置”。
14. 在 **OS 配置** 选项卡上，选择 **Windows** 或 **Linux**。
15. 如果选择 **Windows**，请通过该复选框指定 *Sysprep* 是否已在改计算机上运行。
16. 选择“确定”关闭“OS 配置”边栏选项卡。
17. 选择“确定”以创建自定义映像。
18. 转到[接下来的步骤](#next-steps)部分。

### <a name="upload-a-vhd-file"></a>上传 VHD 文件
若要添加自定义映像，需要 VHD 文件访问权限。

1. 在“VHD 文件”边栏选项卡上，选择“使用 PowerShell 上传VHD 文件”。
   
    ![上传图像](./media/devtest-lab-create-template/upload-image-using-psh.png)
2. 下一个边栏选项卡会显示用于修改和运行 PowerShell 脚本的说明，以便将 VHD 文件上传到 Azure 订阅。 
   **注意：**该过程可能耗时较长，具体取决于 VHD 文件的大小和连接速度。

## <a name="create-a-custom-image-from-a-vm"></a>从 VM 创建自定义映像
如果有已配置的 VM，可以从该虚拟机创建自定义映像，完成后使用该自定义映像来创建其他相同的 VM。 以下步骤演示了如何从 VM 创建自定义映像：

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，然后从列表中选择“开发测试实验室”。
3. 从实验室列表，选择所需的实验室。  
4. 在实验室边栏选项卡上，选择“虚拟机”。
5. 在“虚拟机”边栏选项卡上，选择想要创建自定义映像的 VM。
6. 在虚拟机的边栏选项卡上，选择“创建自定义映像 (VHD)”。
   
    ![创建自定义映像菜单项](./media/devtest-lab-create-template/create-custom-image.png)
7. 在“创建映像”边栏选项卡，输入名称和自定义映像的说明。 创建 VM 时，此信息显示在基项列表中。
   
    ![创建自定义映像边栏选项卡](./media/devtest-lab-create-template/create-custom-image-blade.png)
8. 选择是否在 VM 上运行 sysprep。 如果未在 VM 上运行 sysprep，指定从该自定义映像创建 VM 时是否希望运行 sysprep。
9. 创建自定义映像后，选择“确定”。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相关的博客文章
* [自定义映像或公式吗？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
* [复制 Azure DevTest 实验室间的自定义映像](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>后续步骤
添加完用于创建 VM 的自定义映像后，下一步就是 [将 VM 添加到实验室](devtest-lab-add-vm-with-artifacts.md)。




<!--HONumber=Nov16_HO3-->


