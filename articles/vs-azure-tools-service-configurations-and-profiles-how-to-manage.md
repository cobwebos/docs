---
title: 如何管理服务配置和配置文件 | Microsoft Docs
description: 了解如何使用服务配置和配置文件，这两种配置文件将存储云服务的部署环境设置和发布设置。
services: visual-studio-online
author: ghogen
manager: douge
assetId: 7da8c551-fb06-4057-b5c7-c77f4b39d803
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 8/11/2017
ms.author: ghogen
ms.openlocfilehash: 4f2dce7cdbf81718f9ccf0c1b7d52c6678cc847f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-manage-service-configurations-and-profiles"></a>如何管理服务配置和配置文件
## <a name="overview"></a>概述
发布云服务时，Visual Studio 将配置信息存储在两种配置文件中：服务配置和配置文件。 服务配置（.cscfg 文件）存储 Azure 云服务的部署环境设置。 Azure 在管理云服务时，使用这些配置文件。 另一方面，配置文件（.azurePubxml 文件）存储云服务的发布设置。 这些设置是在使用发布向导时所选内容的记录，并由 Visual Studio 在本地使用。 本主题介绍了如何使用这两种类型的配置文件。

## <a name="service-configurations"></a>服务配置
可以创建多个服务配置，以用于每个部署环境。 例如，可以为本地环境创建用于运行和测试 Azure 应用程序的服务配置，而为生产环境创建另一个服务配置。

可以根据需求添加、删除、重命名和修改这些服务配置。 可以在 Visual Studio 中管理这些服务配置，如下图中所示。

![管理服务配置](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

还可以从角色的属性页打开“管理配置”对话框。 要打开 Azure 项目中角色的属性，请打开该角色的快捷菜单，并选择“属性”。 在“设置”选项卡上，展开“服务配置”列表，并选择“管理”以打开“管理配置”对话框。

### <a name="to-add-a-service-configuration"></a>添加服务配置
1. 在解决方案资源管理器中，打开 Azure 项目的快捷菜单，并选择“管理配置”。
   
    此时会显示“管理服务配置”对话框。
2. 若要添加服务配置，必须创建现有配置的副本。 为此，请从“名称”列表中选择想要复制的配置，并选择“创建副本”。
3. （可选）要为服务配置指定其他名称，请从“名称”列表中选择新的服务配置，并选择“重命名”。 在“名称”文本框中，键入要用于此服务配置的名称，并选择“确定”。
   
    此时名为 ServiceConfiguration.[New Name].cscfg 的新服务配置文件将添加到解决方案资源管理器的 Azure 项目中。

### <a name="to-delete-a-service-configuration"></a>删除服务配置
1. 在解决方案资源管理器中，打开 Azure 项目的快捷菜单，并选择“管理配置”。
   
    此时会显示“管理服务配置”对话框。
2. 要删除服务配置，请从“名称”列表中选择要删除的配置，并选择“删除”。 此时会显示一个对话框框以确认要删除此配置。
3. 选择“删除”。
   
     此时该服务配置文件将从解决方案资源管理器的 Azure 项目中删除。

### <a name="to-rename-a-service-configuration"></a>重命名服务配置
1. 在解决方案资源管理器中，打开 Azure 项目的快捷菜单，并选择“管理配置”。
   
    此时会显示“管理服务配置”对话框。
2. 要重命名服务配置，请从“名称”列表中选择新的服务配置，并选择“重命名”。 在“名称”文本框中，键入要用于此服务配置的名称，并选择“确定”。
   
    此时该服务配置文件的名称会在解决方案资源管理器的 Azure 项目中更改。

### <a name="to-change-a-service-configuration"></a>更改服务配置
* 如果要更改服务配置，请在 Azure 项目中打开要更改的特定角色的快捷菜单，并选择“属性”。 有关详细信息，请参阅[如何：使用 Visual Studio 配置 Azure 云服务的角色](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service)。

## <a name="make-different-setting-combinations-by-using-profiles"></a>使用配置文件指定不同的设置组合
使用配置文件，可以为不同用途使用不同的设置组合自动填写**发布向导**。 例如，可以将一个配置文件用于调试，将另一个配置文件用于发布版本。 在这种情况下，**调试**配置文件应启用 **IntelliTrace** 并选择“调试”配置，并且**发布**配置文件应禁用 **IntelliTrace** 并选择“发布”配置。 还可以使用不同配置文件通过不同存储帐户来部署服务。

第一次运行向导时，将创建默认配置文件。 Visual Studio 将该配置文件存储在扩展名为 .azurePubXml 的文件中，该文件将添加到 Azure 项目的 **Profiles** 文件夹下。 如果稍后运行向导时手动指定其他选项，则会自动更新该文件。 在运行以下过程之前，应该至少已发布了一次云服务。

### <a name="to-add-a-profile"></a>添加配置文件
1. 打开 Azure 项目的快捷菜单，并选择“发布”。
2. 选择“目标配置文件”列表旁边的“保存配置文件”按钮，如下图所示。 这会创建一个配置文件。
   
    ![创建新的配置文件](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)
3. 创建配置文件后，选择“目标配置文件”列表中的“<管理…>”。
   
    此时会显示“管理配置文件”对话框，如下图所示。
   
    ![“管理配置文件”对话框](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)
4. 在“名称”列表中，选择一个配置文件，并选择“创建副本”。
5. 选择“关闭”按钮。
   
    此时新的配置文件将出现在“目标配置文件”列表中。
6. 在“目标配置文件”列表中，选择刚创建的配置文件。 将使用所选配置文件中的选项填充“发布向导”设置。
7. 选择“上一页”和“下一页”按钮以显示发布向导的每一页，并自定义此配置文件的设置。 有关信息，请参阅 [Publish Azure Application Wizard](http://go.microsoft.com/fwlink/p/?LinkID=623085)（发布 Azure 应用程序向导）。
8. 完成自定义设置后，选择“下一步”以返回到“设置”页。 当使用这些设置发布服务时或者选择配置文件列表旁边的“保存”时，将保存该配置文件。

### <a name="to-rename-or-delete-a-profile"></a>重命名或删除配置文件
1. 打开 Azure 项目的快捷菜单，并选择“发布”。
2. 在“目标配置文件”列表中，选择“管理”。
3. 在“管理配置文件”对话框中，选择要删除的配置文件，并选择“删除”。
4. 在出现的确认对话框中，选择“确定”。
5. 选择“关闭”。

### <a name="to-change-a-profile"></a>更改配置文件
1. 打开 Azure 项目的快捷菜单，并选择“发布”。
2. 在“目标配置文件”列表中，选择要更改的配置文件。
3. 选择“上一页”和“下一页”按钮以显示发布向导的每一页，并更改所需的设置。 有关信息，请参阅 [Publish Azure Application Wizard](http://go.microsoft.com/fwlink/p/?LinkID=623085)（发布 Azure 应用程序向导）。
4. 完成更改设置后，选择“下一步”以返回到“设置”。
5. （可选）选择“发布”以使用新设置发布云服务。 如果现在不需要发布云服务，并且关闭发布向导，Visual Studio 会询问是否要保存对配置文件的更改。

## <a name="next-steps"></a>后续步骤
若要了解如何在 Visual Studio 中配置 Azure 项目的其他部分，请参阅[配置 Azure 项目](http://go.microsoft.com/fwlink/p/?LinkID=623075)

