---
title: "使用 Visual Studio 配置 Azure 云服务项目 | Microsoft Docs"
description: "了解如何根据你对该项目的要求在 Visual Studio 中配置 Azure 云服务项目。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 33d6b242c5562bd0aba4786ff70782c697ca7cd7


---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>使用 Visual Studio 配置 Azure 云服务项目
可根据你对 Azure 云服务项目的要求来对其进行配置。 可针对以下类别设置项目的属性：

* **将云服务发布到 Azure**
  
  可以设置属性以确保部署到 Azure 的现有云服务不会被意外删除。
* **在本地计算机上运行或调试云服务**
  
  可以选择要使用的服务配置并指示是否要启动 Azure 存储模拟器。
* **在创建云服务包时对其进行验证**
  
  可以决定将任意警告视为错误，从而确保云服务包正常部署而不出现任何问题。 在部署后发现出现故障时，此操作可以减少你的等待时间。

下图显示了如何选择在本地运行或调试云服务时要使用的配置。 可从此窗口中设置所需的任何项目属性，如下图中所示。

![配置 Microsoft Azure 项目](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## <a name="to-configure-an-azure-cloud-service-project"></a>配置 Azure 云服务项目
1. 若要从“解决方案资源管理器”中配置云服务项目，请打开云服务项目的快捷菜单，然后选择“属性”。
   
   Visual Studio 编辑器中将显示包含云服务项目名称的页面。
2. 选择“开发”选项卡。
3. 为确保不是意外删除 Azure 中的现有部署，在删除现有部署列表之前，请选择 **True**。
4. 若要选择在本地运行或调试云服务时要使用的服务配置，请在“服务配置”列表中选择服务配置。
   
   > [!NOTE]
   > 如果想创建要使用的服务配置，请参阅“如何：管理服务配置和配置文件”。 如果想修改角色的服务配置，请参阅[如何使用 Visual Studio 配置 Azure 云服务的角色](vs-azure-tools-configure-roles-for-cloud-service.md)。
   > 
   > 
5. 若要在本地运行或调试云服务时启动 Azure 存储模拟器，请在“启动 Azure 存储模拟器”中选择“True”。
6. 为确保在出现包验证错误时无法发布，请在“将警告视为错误”中选择“True”。
7. 为确保 Web 角色每次在 IIS Express 中本地启动时都使用同一端口，请在“使用 Web 项目端口”中选择“True”。 若要为特定 Web 项目使用特定端口，请打开该 Web 项目的快捷菜单，依次选择“属性”选项卡、“Web”选项卡，然后在“IIS Express”部分的“项目 URL”设置中更改端口号。 例如，输入 `http://localhost:14020` 作为项目 URL。
8. 若要保存对云服务项目的属性所做的任何更改，请选择工具栏上的“保存”按钮。

## <a name="next-steps"></a>后续步骤
若要了解有关如何在 Visual Studio 中配置 Azure 云服务项目的详细信息，请参阅[使用多种服务配置来配置 Azure 项目](vs-azure-tools-multiple-services-project-configurations.md)。




<!--HONumber=Nov16_HO3-->


