---
title: "在 Azure Stack 中使用 Visual Studio 部署模板 | Microsoft 文档"
description: "了解如何在 Azure Stack 中使用 Visual Studio 部署模板。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: ecefa8c3695aad0593516cb9ad8812216e163192
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>使用 Visual Studio 在 Azure Stack 中部署模板

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

使用 Visual Studio 将 Azure 资源管理器模板部署到 Azure 堆栈开发工具包。

1. 使用 Visual Studio [安装并连接](azure-stack-install-visual-studio.md)到 Azure Stack。
2. 打开 Visual Studio。
3. 依次单击“文件”、“新建”，并在“新建项目”对话框中单击“Azure 资源组”。
4. 输入新项目的名称，并单击“确定”。
5. 在“选择 Azure 模板”对话框中，将“显示此位置的模板”下拉列表项更改为“Azure Stack 快速启动”
6. 单击**101-创建的存储-帐户**，然后单击**确定**。  
7. 在新项目中，展开“解决方案资源管理器”窗格中的“模板”节点可以查看可用模板的列表。
8. 在“解决方案资源管理器”窗格中，右键单击项目的名称，单击“部署”，并单击“新建部署”。
9. 在“部署到资源组”对话框中的“订阅”下拉列表内，选择 Microsoft Azure Stack 订阅。
10. 在“资源组”列表中，选择现有资源组或创建新资源组。
11. 在“资源组位置”列表中选择一个位置，并单击“部署”。
12. 在“编辑参数”对话框中输入参数的值（根据模板输入不同的值），并单击“保存”。

## <a name="next-steps"></a>后续步骤
[通过命令行部署模板](azure-stack-deploy-template-command-line.md)

[为 Azure Stack 开发模板](azure-stack-develop-templates.md)

