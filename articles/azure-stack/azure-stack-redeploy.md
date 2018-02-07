---
title: "重新部署 Azure 堆栈 |Microsoft 文档"
description: "重新部署 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>重新部署 Azure 堆栈
如果在部署 Azure 堆栈时收到错误，你可以重新运行安装程序使用以下 PowerShell 命令： `.\InstallAzureStackpoc.ps1 -rerun`。 此命令将重新启动而无需从头开始通过以前失败的点处的 Azure 堆栈安装程序。 如果再次收到相同的安装程序错误，可能需要执行完全重新部署到地址问题。 

若要重新部署 Azure 堆栈，你必须通过从头开始如下所述。

## <a name="steps-to-redeploy-azure-stack"></a>若要重新部署 Azure 堆栈的步骤
1. 开发工具包在主机上，打开提升的 PowerShell 控制台 > 导航到 asdk installer.ps1 脚本 > 运行它 > 单击**重新启动**。
2. 选择基操作系统 (不**Azure 堆栈**)，然后单击**下一步**。
3. 开发工具包主机重新启动后，删除 CloudBuilder.vhdx 文件已用作以前的部署的一部分。
4. [部署开发工具包](azure-stack-run-powershell-script.md)。

## <a name="next-steps"></a>后续步骤
[连接到 Azure Stack](azure-stack-connect-azure-stack.md)

