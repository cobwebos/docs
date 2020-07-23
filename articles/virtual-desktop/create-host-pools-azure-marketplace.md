---
title: 使用 Azure 门户创建 Windows 虚拟桌面主机池 - Azure
description: 如何使用 Azure 门户创建 Windows 虚拟桌面主机池。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7c22ad844f0a543cfbf2e007ab2bfc8f0fe22ef7
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362928"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>教程：使用 Azure 门户创建主机池

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 2020 春季更新版。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面 2019 秋季版，请参阅[此文](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md)。 使用 Windows 虚拟桌面 2019 秋季版创建的任何项目都无法通过 Azure 门户进行管理。
>
> Windows 虚拟桌面 2020 春季更新版目前为公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

主机池是 Windows 虚拟桌面环境中包含一个或多个相同虚拟机 (VM) 的集合。 每个主机池可以包含一个应用组，用户可以像在物理桌面上一样与该应用组交互。

本文将引导你完成通过 Azure 门户为 Windows 虚拟桌面环境创建主机池的设置过程。 此方法提供基于浏览器的用户界面，用于在 Windows 虚拟桌面中创建主机池，在 Azure 订阅中创建包含 VM 的资源组，将这些 VM 加入 Azure Active Directory (AD) 域，然后将 VM 注册到 Windows 虚拟桌面。

## <a name="prerequisites"></a>先决条件

需要输入以下参数才能创建主机池：

- VM 映像名称
- VM 配置
- 域和网络属性
- Windows 虚拟桌面主机池属性

还需要了解以下信息：

- 要使用的映像的源所在的位置。 该映像是来自 Azure 库，还是一个自定义映像？
- 你的域加入凭据。

此外，请确保已注册 Microsoft.DesktopVirtualization 资源提供程序。 如果还没有注册，请转到“订阅”，选择你的订阅名称，然后选择“Azure 资源提供程序” 。

使用 Azure 资源管理器模板创建 Windows 虚拟桌面主机池时，可以从 Azure 库、托管映像或非托管映像创建虚拟机。 若要详细了解如何创建 VM 映像，请参阅[准备要上传到 Azure 的 Windows VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md)，以及[为 Azure 中的通用化 VM 创建托管映像](../virtual-machines/windows/capture-image-resource.md)。

如果你还没有 Azure 订阅，请务必在按照以下说明开始操作之前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="begin-the-host-pool-setup-process"></a>开始执行主机池设置过程

若要开始创建新的主机池，请执行以下操作：

1. 在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。

2. 在搜索栏中输入“Windows 虚拟桌面”，然后在“服务”下找到并选择“Windows 虚拟桌面” 。

3. 在“Windows 虚拟桌面”概览页中，选择“创建主机池” 。

4. 在“基本信息”选项卡中，在“项目详细信息”下选择正确的订阅。

5. 选择“新建”以创建新的资源组，或者从下拉菜单中选择现有的资源组。

6. 为你的主机池输入一个唯一名称。

7. 在“位置”字段中，从下拉菜单中选择要在其中创建主机池的区域。
   
   与你选择的区域关联的 Azure 地理位置是此主机池及其相关对象的元数据将存储到的位置。 请确保在要用于存储服务元数据的地理位置选择区域。

     > [!div class="mx-imgBorder"]
     > ![Azure 门户的屏幕截图，其中显示了“位置”字段并选择了“美国东部”位置。 字段旁边的文本指出“将在美国东部存储元数据”。](media/portal-location-field.png)

8. 在“主机池类型”下，选择主机池是“个人”还是“共用”类型 。

    - 如果选择“个人”，请在“分配类型”字段中选择“自动”或“直接”  。

      > [!div class="mx-imgBorder"]
      > ![“分配类型”字段下拉菜单的屏幕截图。 用户已选择“自动”。](media/assignment-type-field.png)

9. 如果选择“共用”，请输入以下信息：

     - 对于“会话数上限”，请输入你要在单个会话主机中进行负载均衡的最大用户数。
     - 对于“负载均衡算法”，请根据使用模式选择“广度优先”或“深度优先”。

       > [!div class="mx-imgBorder"]
       > ![“分配类型”字段的屏幕截图，其中已选择“共用”。 用户正在将其光标悬停在负载均衡下拉菜单中的“广度优先”上。](media/pooled-assignment-type.png)

10. 在完成时选择“下一步:VM 详细信息”。

11. 如果你已创建虚拟机并想要在新主机池中使用它们，请选择“否”。 如果要创建新的虚拟机并将其注册到新主机池，请选择“是”。

现在，第一部分已完成，接下来让我们转到设置过程的下一部分来创建 VM。

## <a name="virtual-machine-details"></a>虚拟机详细信息

现在，我们已完成第一部分，接下来必须设置 VM。

若要在主机池设置过程中设置虚拟机，请执行以下操作：

1. 在“资源组”下，选择要在其中创建虚拟机的资源组。 此资源组可以与用于主机池的资源组不同。

2. 选择要在其中创建虚拟机的“虚拟机区域”。 该区域可以与你为主机池选择的区域相同或不同。

3. 接下来，选择要创建的虚拟机的大小。 可以按原样保留默认大小，也可以选择“更改大小”来更改大小。 如果选择“更改大小”，请在显示的窗口中选择适合你的工作负荷的虚拟机大小。

4. 在“VM 数量”下，提供要为主机池创建的 VM 数量。

    >[!NOTE]
    >在设置主机池时，设置过程最多可以创建 400 个 VM，每个 VM 设置过程将在资源组中创建四个对象。 由于创建过程不会检查订阅配额，因此请确保输入的 VM 数量在资源组和订阅的 Azure VM 与 API 限制范围内。 创建完主机池后，可以添加更多的 VM。

5. 然后，提供一个名称前缀来为设置过程创建的虚拟机命名。 该后缀是 `-` 加上从 0 开始的数字。

6. 接下来，选择需要用来创建虚拟机的映像。 可以选择“库”或“存储 Blob” 。

    - 如果选择了“库”，请从下拉菜单中选择一个建议的映像：

      - Windows 10 企业版多会话版本 1909 + Microsoft 365 企业应用版 - Gen 1
      - Windows 10 企业版多会话版本 1909 – Gen 1
      - Windows Server 2019 Datacenter - Gen1

     如果未看到所需的映像，请选择“浏览所有映像和磁盘”，然后便可以选择库中的另一个映像，或选择 Microsoft 和其他发行商提供的映像。

     > [!div class="mx-imgBorder"]
     > ![市场屏幕截图，其中显示了 Microsoft 提供的映像列表。](media/marketplace-images.png)

     还可以转到“我的项”并选择已上传的自定义映像。

     > [!div class="mx-imgBorder"]
     > ![“我的项”选项卡的屏幕截图。](media/my-items.png)

    - 如果选择“存储 Blob”，则可以利用你自己的、通过 Hyper-V 生成的映像或 Azure VM 上的映像。 只需输入该映像在存储 Blob 中的位置作为 URI 即可。

7. 选择希望 VM 使用的 OS 磁盘类型：“标准 SSD”、“高级 SSD”或“标准 HDD”。

8. 在“网络和安全”下，选择要在其中放置所创建的虚拟机的虚拟网络和子网。 请确保该虚拟网络可以连接到域控制器，因为你需要将该虚拟网络中的虚拟机加入域。 接下来，选择是否要为虚拟机使用公共 IP。 建议选择“否”，因为专用 IP 更安全。

9. 选择所需的安全组类型：“基本”、“高级”或“无”  。

    如果选择“基本”，则必须选择是否需要打开任何入站端口。 如果选择“是”，请从标准端口列表中选择允许入站连接的端口。

    >[!NOTE]
    >为了提高安全性，我们建议不要打开公共入站端口。

    > [!div class="mx-imgBorder"]
    > ![安全组页的屏幕截图，其中的下拉菜单显示了可用端口列表。](media/available-ports.png)
    
    如果选择“高级”，请选择已配置的现有网络安全组。

10. 然后，选择是否要将虚拟机加入特定的域和组织单位。 如果选择“是”，请指定要加入到的域。 还可以添加要将虚拟机加入到的特定组织单位。

11. 在“管理员帐户”下，输入所选虚拟网络的 Active Directory 域管理员的凭据。

12. 选择“工作区”。

完成这些操作后，我们便可以开始进行设置主机池的下一阶段：将应用组注册到工作区。

## <a name="workspace-information"></a>工作区信息

主机池设置过程默认会创建一个桌面应用程序组。 要使主机池按预期方式工作，需将此应用组发布给用户或用户组，并且必须将此应用组注册到工作区。 

若要将桌面应用组注册到工作区，请执行以下操作：

1. 请选择“是”。

   如果选择“否”，则可以在以后再注册应用组，但我们建议尽快完成工作区注册，使主机池能够正常工作。

2. 接下来，选择是要创建新的工作区，还是从现有工作区进行选择。 只允许将应用组注册到在主机池所在的同一位置中创建的工作区。

3. （可选）可以选择“标记”。

    可以在此处添加标记，以便可以根据元数据将对象分组，为管理员的工作提供方便。

4. 完成操作后，选择“查看 + 创建”。 

     >[!NOTE]
     >“查看 + 创建”过程不会检查你的密码是否符合安全标准，也不会检查你的体系结构是否正确，因此，你需要亲自检查密码和体系结构是否存在任何问题。 

5. 检查你的部署的相关信息，确保全部正确。 完成操作后，选择“创建”。 这会启动部署过程，该过程将创建以下对象：

     - 新的主机池。
     - 桌面应用组。
     - 工作区（如果已选择创建工作区）。
     - 如果你已选择注册桌面应用组，则会完成注册
     - 已加入域并已注册到新主机池的虚拟机（如果已选择创建虚拟机）。
     - 基于配置的 Azure 资源管理模板的下载链接。

所有操作现已全部完成！

## <a name="next-steps"></a>后续步骤

创建主机池后，可以使用 RemoteApp 程序来填充主机池。 若要详细了解如何在 Windows 虚拟桌面中管理应用，请转到下一篇教程：

> [!div class="nextstepaction"]
> [管理应用组教程](./manage-app-groups.md)
