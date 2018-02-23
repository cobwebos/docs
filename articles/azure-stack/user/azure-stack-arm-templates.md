---
title: "在 Azure 堆栈中使用 Azure 资源管理器模板 |Microsoft 文档"
description: "了解如何使用 Azure 资源管理器模板在 Azure 堆栈中来设置资源。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 6d4ef16881ef8dc249116aec706f760b163a2972
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>在 Azure 堆栈中使用 Azure 资源管理器模板

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 资源管理器模板部署和设置在单个协调的操作的应用程序的所有资源。 你还可以重新部署模板，以对资源组中的资源进行更改。

可以使用 Microsoft Azure 堆栈门户、 PowerShell、 命令行和 Visual Studio 部署这些模板。

以下快速入门模板都位于[GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>部署 SharePoint （非高可用性）
使用 PowerShell DSC 扩展来创建一个 SharePoint 2013 场，包括以下资源：

* 虚拟网络
* 三个存储帐户
* 两个外部负载平衡器
* 一个 VM 配置为具有单个域的新林的域控制器
* 一个 VM 配置为 SQL Server 2014 的独立服务器
* 一个 VM 配置为一台计算机 SharePoint 2013 场

## <a name="deploy-ad-non-high-availability"></a>部署 AD （非高可用性）
使用 PowerShell DSC 扩展来创建包含以下资源的 AD 域控制器服务器：

* 虚拟网络
* 一个存储帐户
* 一个外部负载平衡器
* 一个 VM 配置为具有单个域的新林的域控制器

## <a name="deploy-adsql-non-high-availability"></a>部署 AD/SQL （非高可用性）
使用 PowerShell DSC 扩展来创建的 SQL Server 2014 独立服务器，包括以下资源：

* 虚拟网络
* 两个存储帐户
* 一个外部负载平衡器
* 一个 VM 配置为具有单个域的新林的域控制器
* 一个 VM 配置为 SQL Server 2014 的独立服务器

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server
使用 PowerShell DSC 扩展配置现有虚拟机本地配置管理器 (LCM) 并将其注册到 Azure 自动化帐户 DSC 请求服务器。

## <a name="create-a-virtual-machine-from-a-user-image"></a>从用户映像创建虚拟机
从自定义用户映像创建虚拟机。 此模板还会部署 （使用 DNS) 的虚拟网络、 公共 IP 地址和网络接口。

## <a name="simple-vm"></a>简单的 VM
部署的 Windows VM，包括 （具有 DNS) 的虚拟网络、 公共 IP 地址和网络接口。

## <a name="cancel-a-running-template-deployment"></a>取消正在运行的模板部署
若要取消正在运行的模板部署，使用`Stop-AzureRmResourceGroupDeployment`PowerShell cmdlet。

## <a name="next-steps"></a>后续步骤
[通过门户部署模板](azure-stack-deploy-template-portal.md)

[Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md)

