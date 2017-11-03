---
title: "重新部署 Azure 堆栈 |Microsoft 文档"
description: "重新部署 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 891cde9b16bbbb51729129b6ad7a0f3794307baa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-azure-stack"></a>重新部署 Azure 堆栈
若要重新部署 Azure 堆栈，你必须通过从头开始如下所述。

## <a name="steps-to-redeploy-azure-stack"></a>若要重新部署 Azure 堆栈的步骤
1. 开发工具包在主机上，打开提升的 PowerShell 控制台 > 导航到 asdk installer.ps1 脚本 > 运行它 > 单击**重新启动**。
2. 选择基操作系统 (不**Azure 堆栈**)，然后单击**下一步**。
3. 开发工具包主机重新启动后，删除 CloudBuilder.vhdx 文件已用作以前的部署的一部分。
4. [部署开发工具包](azure-stack-run-powershell-script.md)。

## <a name="next-steps"></a>后续步骤
[连接到 Azure Stack](azure-stack-connect-azure-stack.md)

