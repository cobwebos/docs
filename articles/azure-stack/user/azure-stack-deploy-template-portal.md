---
title: 使用 Azure Stack 门户部署模板 | Microsoft Docs
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
ms.date: 05/24/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: eea4f568f21693764222c8fdbe3316bf6008cc05
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604227"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>使用 Azure Stack 门户部署模板

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用门户将 Azure 资源管理器模板部署到 Azure Stack。

## <a name="to-deploy-a-template"></a>若要部署模板

1. 登录到门户中，依次选择“新建”、“自定义”。
2. 选择“模板部署”。
3. 选择“编辑模板”，然后将 JSON 模板代码粘贴到代码窗口中。 选择“保存”。
4. 选择“编辑参数”，为显示的参数提供值，然后选择“确定”。
5. 选择“订阅”。 选择要使用的订阅，然后选择“确定”。
6. 选择“资源组”。 选择现有资源组，或创建一个新资源组，然后选择“确定”。
7. 选择**创建**。 仪表板上的新磁贴会跟踪模板部署的进度。

## <a name="next-steps"></a>后续步骤

* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
