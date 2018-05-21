---
title: 部署模板使用 Azure 堆栈中的门户 |Microsoft 文档
description: 了解如何使用 Azure Stack 门户部署模板。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 278f15271d3a5443102f5e387d3db1adb53fe7db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>使用 Azure Stack 门户部署模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

门户可用于将 Azure 资源管理器模板部署到 Azure 堆栈。

若要部署模板：

1. 登录到门户中，选择**新建**，然后选择**自定义**。
2. 选择**模板部署**。
3. 选择**编辑模板**，然后将 JSON 模板代码粘贴到代码窗口。 选择“保存”。
4. 选择**编辑参数**，提供值的参数，将显示，然后选择**确定**。
5. 选择**订阅**。 选择你想要使用，然后选择的订阅**确定**。
6. 选择**资源组**。 选择现有资源组或创建一个新，然后选择**确定**。
7. 选择**创建**。 仪表板上的新磁贴会跟踪模板部署的进度。

## <a name="next-steps"></a>后续步骤

[通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
