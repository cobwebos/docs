---
title: 教程：创建内容库以在 Azure VMware 解决方案 (AVS) 中部署 VM
description: 本 Azure VMware 解决方案 (AVS) 教程介绍如何创建内容库以在 AVS 私有云中部署 VM。
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: bdea4304baa92fadc4d6c3d0060f99cf37af5f77
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259411"
---
# <a name="tutorial-create-a-content-library-to-deploy-vms-in-azure-vmware-solution-avs"></a>教程：创建内容库以在 Azure VMware 解决方案 (AVS) 中部署 VM

内容库以库项的形式存储和管理内容。 单个库项由用于部署虚拟机 (VM) 的一个或多个文件组成。 
 
在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 创建内容库
> * 将 ISO 映像上传到内容库
> * 使用内容库中的 ISO 部署 VM

## <a name="prerequisites"></a>先决条件

学习本教程需要具备 NSX-T 逻辑交换机段和托管式 DHCP 服务。  有关详细信息，请参阅[如何在 Azure VMWare 解决方案 (AVS) 预览版中管理 DHCP](manage-dhcp.md)。

## <a name="create-a-content-library"></a>创建内容库

1. 在本地 vSphere 客户端中，选择“菜单”>“内容库”。

   ![选择“菜单”>“内容库”](./media/content-library/vsphere-menu-content-libraries.png)

1. 单击“添加”按钮新建一个内容库。

   ![单击“添加”按钮新建一个内容库。](./media/content-library/create-new-content-library.png)

1. 指定一个名称，确认 vCenter 服务器的 IP 地址，然后选择“下一步”。

   ![指定名称和备注，然后选择“下一步”。](./media/content-library/new-content-library-step1.png)

1. 选择“本地内容库”，然后选择“下一步” 。

   ![在本示例中，我们选择创建一个本地内容库，然后选择“下一步”。](./media/content-library/new-content-library-step2.png)

1. 选择用于存储内容库的数据存储，然后选择“下一步”。

   ![选择要用于托管内容库的数据存储，然后选择下一步。](./media/content-library/new-content-library-step3.png)

1. 查看并验证内容库设置，然后选择“完成”。

   ![验证设置，然后选择“完成”。](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>将 ISO 映像上传到内容库

现在内容库已创建完成，可以添加 ISO 映像来将 VM 部署到私有云群集。 

1. 在 vSphere 客户端中，选择“菜单”>“内容库”。

1. 右键单击要用于新 ISO 的内容库，然后选择“导入项目”。

1. 通过以下一种方式从源导入一个库项，然后选择“导入”：
   1. 选择“URL”并提供用于下载 ISO 的 URL。

   1. 选择“本地文件”从本地系统中上传。

   > [!TIP]
   > （可选）可以自定义“目标”的名称和备注。

1. 打开库，然后选择“其他类型”选项卡以验证 ISO 已成功上传。


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>将 VM 部署到私有云群集

1. 在 vSphere 客户端中，选择“菜单”>“主机和群集”。

1. 在左侧窗格中，展开树结构，然后选择一个群集。

1. 选择“操作”>“新建虚拟机”。

1. 完成向导并修改所需的设置。

1. 选择“新建 CD/DVD 驱动器”>“客户端设备”>“内容库 ISO 文件”。

1. 选择上一部分中上传的 ISO，然后选择“确定”。

1. 选择“连接”复选框以在开机时装载此 ISO。

1. 选择“新建网络”>“选择”下拉列表>“浏览”。

1. 选择“逻辑交换机(段)”，然后选择“确定” 。

1. 修改任何其他硬件设置，然后选择“下一步”。

1. 验证设置，然后选择“完成”。


## <a name="next-steps"></a>后续步骤

如果计划使用混合云扩展 (HCX) 将 VM 工作负荷迁移到私有云，请按照[安装 HCX 以使用 Azure VMware 解决方案](hybrid-cloud-extension-installation.md)这一流程操作。

<!-- LINKS - external-->

<!-- LINKS - internal -->