---
title: "将模板与 Azure 堆栈中的门户部署 |Microsoft 文档"
description: "了解如何使用 Azure 堆栈门户部署模板。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 30c077747685ad7617148e9130926120420b098b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>部署模板使用 Azure 堆栈门户

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

使用门户将 Azure 资源管理器模板部署到 Azure 堆栈开发工具包。

资源管理器模板部署和设置在单个协调的操作的应用程序的所有资源。

1. 登录到门户，单击**新建**，单击**自定义**，然后单击**模板部署**。
2. 单击**编辑模板**，然后将 JSON 模板代码粘贴到边栏选项卡，，然后单击**保存**。
3. 单击**编辑参数**，键入列出，参数的值，然后单击**确定**。
4. 单击**订阅**，选择你想要使用，并依次的订阅**确定**。
5. 单击**资源组**，选择现有资源组或创建一个新，然后单击**确定**。
6. 单击“创建”。 仪表板上的新磁贴跟踪模板部署的进度。

## <a name="next-steps"></a>后续步骤
[通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)

