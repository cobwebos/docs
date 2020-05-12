---
title: 使用门户管理 Windows 虚拟桌面的应用组 - Azure
description: 如何使用 Azure 门户管理 Windows 虚拟桌面应用组。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f072ed8a758173645c886cabf0b20f9e123cbbab
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612102"
---
# <a name="tutorial-manage-app-groups-with-the-azure-portal"></a>教程：使用 Azure 门户管理应用组

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 2020 春季更新版。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面 2019 秋季版，请参阅[此文](./virtual-desktop-fall-2019/manage-app-groups-2019.md)。
>
> Windows 虚拟桌面 2020 春季更新版目前为公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

为新的 Windows 虚拟桌面主机池创建的默认应用组也会发布完整桌面。 此外，可为主机池创建一个或多个 RemoteApp 应用程序组。 请遵循本教程创建 RemoteApp 应用组并发布单个“开始”菜单应用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 RemoteApp 组。
> * 授予对 RemoteApp 程序的访问权限。

## <a name="create-a-remoteapp-group"></a>创建 RemoteApp 组

如果已使用 Azure 门户或 PowerShell 创建了主机池和会话主机 VM，可以使用以下过程从 Azure 门户添加应用程序组：

1.  登录 [Azure 门户](https://portal.azure.com/)。

2.  搜索并选择“Windows 虚拟桌面”  。

3.  在页面左侧的菜单中选择“应用程序组”，然后选择“+ 添加”   。

4. 在“基本信息”选项卡上，选择要为其创建应用组的订阅组和资源组  。 也可以选择创建新的资源组，而不是选择现有的资源组。

5. 从“主机池”旁边的下拉菜单中选择要与应用程序组关联的主机池  。

    >[!NOTE]
    >必须选择与应用程序组关联的主机池。 应用组包含从会话主机提供的应用或桌面，会话主机是主机池的一部分。 在创建过程中，应用组需要与主机池关联。

    > [!div class="mx-imgBorder"]
    > ![Azure 门户中“基本信息”选项卡的屏幕截图。](media/basics-tab.png)

6. 若要将应用程序组添加到主机池，请在屏幕左侧的菜单中选择“主机池”  。
   
    接下来，选择要将应用程序组添加到的主机池的名称。
   
    然后，在屏幕左侧的菜单中选择“应用程序组”，然后选择“+添加”   。

    最后，选择要在其中创建应用组的订阅组和资源组。 可以从下拉菜单中选择现有资源组的名称，或者选择“新建”以创建新的资源组  。

      >[!NOTE]
      >向主机池添加应用程序组时，与应用程序组相关的主机池已被选中，因为你是从该主机池导航的。
      > 
      > [!div class="mx-imgBorder"]
      >![已预先选择主机池的“基本信息”选项卡的屏幕截图。](media/host-pool-selected.png)

7. 在“应用程序组类型”下选择“RemoteApp”，然后为 RemoteApp 输入名称  。

      > [!div class="mx-imgBorder"]
      > ![“应用程序组类型”字段的屏幕截图。 其中突出显示了“RemoteApp”。](media/remoteapp-button.png)

8.  选择“分配”选项卡  。

9.  若要将各个用户或用户组发布到应用组，请选择“+添加 Azure AD 用户或用户组”  。

10.  选择要将应用添加到的用户的数量。 可以选择单个或多个用户和用户组。

     > [!div class="mx-imgBorder"]
     > ![用户选择菜单的屏幕截图。](media/select-users.png)

11.  选择“选择”  。

12.  选择“应用程序”选项卡，然后选择“+添加应用程序”   。

13.  若要从开始菜单添加应用程序，请执行以下操作： 

      - 转到“应用程序源”，从下拉菜单中选择“开始菜单”   。 接下来，转到“应用程序”，从下拉菜单中选择应用程序  。

     > [!div class="mx-imgBorder"]
     > ![“添加应用程序”屏幕的屏幕截图，其中已选择“开始菜单”。](media/add-app-start.png)

      - 在“显示名称”中，输入要在用户客户端上向用户显示的应用程序名称  。

      - 将其他选项保持原样，然后选择“保存”  。

14. 若要从特定文件路径添加应用程序，请执行以下操作：

      - 转到“应用程序源”，从下拉菜单中选择“文件路径”   。

      - 输入应用程序在会话主机上的路径，该主机已注册到关联的主机池。

      - 在“应用程序名称”、“显示名称”、“图标路径”和“图标索引”字段中输入应用程序的详细信息     。

      - 选择“保存”。 

     > [!div class="mx-imgBorder"]
     > ![“添加应用程序”页的屏幕截图，其中已选择文件路径。](media/add-app-file.png)

     为要添加到应用程序组中的每个应用程序重复此过程。

15.  接下来，选择“工作区”选项卡  。

16.  若要将应用组注册到工作区，请转到“注册应用程序组”并选择“是”   。 如果想在以后注册应用组，请选择“否”  。

17.  如果选择“是”，可以选择要向其中注册应用组的现有工作区  。
       
       >[!NOTE]
       >只能将应用组注册到在主机池所在的位置中创建的工作区。 此外， 如果以前已将同一主机池中的另一应用组作为新应用组注册到工作区，则系统会选择该应用组，并且你无法对其进行编辑。 必须将主机池中的所有应用组注册到同一个工作区。

     > [!div class="mx-imgBorder"]
     > ![现有工作区的“注册应用程序组”页的屏幕截图。 已预先选择主机池。](media/register-existing.png)

18. （可选）若要创建标记以使工作区易于组织，请选择“标记”选项卡，然后输入标记名称  。

19. 完成后，选择“审阅 + 创建”  选项卡。

20. 稍候片刻，让验证过程完成。 完成验证后，选择“创建”以部署应用组  。

部署过程将为你执行以下操作：

- 创建 RemoteApp 应用组。
- 将你选择的应用添加到应用组。
- 将应用组发布到你选择的用户和用户组。
- 注册应用组（如果你选择这样做）。
- 基于你的配置创建 Azure 资源管理器模板的链接，你可以下载并保存该模板供以后使用。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建应用组、使用 RemoteApps 程序来填充它，以及如何向应用组分配用户。 若要了解如何创建验证主机池，请参阅以下教程。 在将服务更新部署到生产环境之前，可以使用验证主机池来监视服务更新。

> [!div class="nextstepaction"]
> [创建主机池以验证服务更新](./create-validation-host-pool.md)
