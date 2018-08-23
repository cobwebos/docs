---
title: 使用 Azure Stack 门户部署模板 | Microsoft Docs
description: 了解如何使用 Azure Stack 门户部署模板。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 931c3d8beb9f2ed12228c74f09f84bbdee1798b8
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2018
ms.locfileid: "42139575"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>使用 Azure Stack 门户部署模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用门户将 Azure 资源管理器模板部署到 Azure Stack。

## <a name="to-deploy-a-template"></a>部署模板

1. 登录到门户中，依次选择“新建”、“自定义”。
2. 选择“模板部署”。
3. 选择“编辑模板”，然后将 JSON 模板代码粘贴到代码窗口中。 选择“保存”。
4. 选择“编辑参数”，为显示的参数提供值，然后选择“确定”。
5. 选择“订阅”。 选择要使用的订阅，然后选择“确定”。
6. 选择“资源组”。 选择现有资源组，或创建一个新资源组，然后选择“确定”。
7. 选择**创建**。 仪表板上的新磁贴会跟踪模板部署的进度。

## <a name="next-steps"></a>后续步骤

* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
