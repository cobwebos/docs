---
title: "使用 Visual Studio 访问私有 Azure 云 | Microsoft Docs"
description: "了解如何通过使用 Visual Studio 访问私有云资源。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 26fea09810e4a5ed1dc19123a5354905ec3e37ea


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>使用 Visual Studio 访问私有 Azure 云
## <a name="overview"></a>概述
默认情况下，Visual Studio 支持公有 Azure 云 REST 终结点。 但如果将 Visual Studio 用于私有 Azure 云，这可能是个问题。 可以使用证书将 Visual Studio 配置为访问私有 Azure 云 REST 终结点。 可通过 Azure 发布设置文件获取这些证书。

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>在 Visual Studio 中访问私有 Azure 云
1. 在私有云的 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)中下载发布设置文件，或与管理员联系以获取发布设置文件。 在 Azure 的公共版中，用于下载的链接是 [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/)。 （下载的文件的扩展名应为 .publishsettings。）
2. 在 Visual Studio 中的“服务器资源管理器”中，选择“Azure”节点，然后在快捷菜单上选择“管理订阅”命令。
   
    ![管理订阅命令](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)
3. 在“管理 Microsoft Azure 订阅”对话框中，选择“证书”选项卡，然后选择“导入”按钮。
   
    ![导入 Azure 证书](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)
4. 在“导入 Microsoft Azure 订阅”对话框中，浏览到保存发布设置文件的文件夹，选择文件，然后选择“导入”按钮。 这会将发布设置文件中的证书导入到 Visual Studio 中。 现在，应该能够与你的私有云资源进行交互。
   
    ![导入发布设置](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>后续步骤
[从 Visual Studio 发布到 Azure 云服务](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[如何：下载并导入发布设置和订阅信息](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)




<!--HONumber=Nov16_HO3-->


