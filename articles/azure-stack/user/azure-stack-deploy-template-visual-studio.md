---
title: 在 Azure Stack 中使用 Visual Studio 部署模板 | Microsoft 文档
description: 了解如何在 Azure Stack 中使用 Visual Studio 部署模板。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4101567eff68789fe5d46a01de26f6a873b519fa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>使用 Visual Studio 在 Azure Stack 中部署模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以使用 Visual Studio 将 Azure 资源管理器模板部署到 Azure 堆栈。

若要部署模板：

1. 使用 Visual Studio [安装并连接](azure-stack-install-visual-studio.md)到 Azure Stack。
2. 打开 Visual Studio。
3. 选择**文件**，然后选择**新建**。 在**新项目**，选择**Azure 资源组**。
4. 输入**名称**作为新项目，然后选择**确定**。
5. 在**选择 Azure 模板**，选取**Azure 堆栈快速入门**从下拉列表。
6. 选择**101-创建的存储-帐户**，然后选择**确定**。
7. 在新项目中，展开**模板**中的节点**解决方案资源管理器**若要查看可用的模板。
8. 在**解决方案资源管理器**，选取你项目的名称，然后选择**部署**。 选择**新部署**。
9. 在**部署到资源组**，使用**订阅**下拉列表来选择你的 Microsoft Azure 堆栈订阅。
10. 从**资源组**列表中，选择现有资源组或创建一个新。
11. 从**资源组位置**列表中，选择一个位置，然后选择**部署**。
12. 在**编辑参数**，提供值的参数 （因模板），然后选择**保存**。

## <a name="next-steps"></a>后续步骤

* [通过命令行部署模板](azure-stack-deploy-template-command-line.md)
* [为 Azure Stack 开发模板](azure-stack-develop-templates.md)
