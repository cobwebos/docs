---
title: "将模板与 Azure 堆栈中的命令行部署 |Microsoft 文档"
description: "了解如何使用跨平台命令行界面 (CLI)，将模板部署到 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 88e10d98a709ff967e19bfe5bfb35bbce9d2df9f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>将 Azure 堆栈使用命令行中的模板部署

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

使用命令行，将 Azure 资源管理器模板部署到 Azure 堆栈开发工具包。 Azure 资源管理器模板部署和设置在单个协调的操作的应用程序的所有资源。

## <a name="before-you-begin"></a>开始之前
 - [安装并连接](azure-stack-connect-cli.md)到使用 Azure CLI 的 Azure 堆栈
 - 下载文件*azuredeploy.json*和*azuredeploy.parameters.json*从[创建存储帐户的示例模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account)。
 
## <a name="deploy-template"></a>部署模板
导航到的文件夹，其中这些文件已下载并运行以下命令来部署模板：

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

此命令将模板部署到资源组**cliRG** Azure 堆栈 POC 默认位置中。

## <a name="validate-template-deployment"></a>验证模板部署
若要查看此资源组和存储帐户，请使用以下命令：

    azure group list

    azure storage account list




