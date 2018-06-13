---
title: 使用 Visual Studio 配置 Azure 云服务项目 | Microsoft Docs
description: 了解如何根据你对该项目的要求在 Visual Studio 中配置 Azure 云服务项目。
services: visual-studio-online
author: ghogen
manager: douge
assetId: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/06/2017
ms.author: ghogen
ms.openlocfilehash: 0b2b39897407875d2722c7fbaebebc1698f931a9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792165"
---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>使用 Visual Studio 配置 Azure 云服务项目
可根据你对 Azure 云服务项目的要求来对其进行配置。 可针对以下类别设置项目的属性：

- **将云服务发布到 Azure** - 可以设置属性以确保部署到 Azure 的现有云服务不会被意外删除。
- **在本地计算机上运行或调试云服务** - 可以选择要使用的服务配置并指示是否要启动 Azure 存储模拟器。
- **在创建云服务包时对其进行验证** - 可以决定将任意警告视为错误，从而确保云服务包正常部署而不出现任何问题。 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>配置 Azure 云服务项目的步骤
1. 在 Visual Studio 中打开或创建 Azure 云服务项目

1. 在“解决方案资源管理器”中右键单击项目，并从上下文菜单中选择“属性”。
   
1. 在项目的属性页中，选择“开发”选项卡。

    ![“项目属性”菜单](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. 将“删除现有部署前提示”设为“True”。 此设置有助于确保不会意外删除 Azure 中的现有部署

1. 若要指示在本地运行或调试云服务时要使用的服务配置，请选择所需**服务配置**。 有关如何修改角色的服务配置的详细信息，请参阅[如何使用 Visual Studio 配置 Azure 云服务的角色](./vs-azure-tools-configure-roles-for-cloud-service.md)。

1. 要在本地运行或调试云服务时启动 Azure 存储模拟器，请将“启动 Azure 存储模拟器”设为“True”。

1. 要确保在出现包验证错误时无法发布，请将“将警告视为错误”设为“True”。

1. 要确保 Web 角色每次在 IIS Express 中本地启动时都使用同一端口，请将“使用 Web 项目端口”设为“True”。

1. 从 Visual Studio 工具栏中，选择“保存”。

## <a name="next-steps"></a>后续步骤
- [使用多种服务配置来配置 Azure 项目](vs-azure-tools-multiple-services-project-configurations.md)

