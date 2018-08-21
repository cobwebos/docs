---
title: 对 Azure Stack 验证作为服务进行故障排除 |Microsoft Docs
description: 对验证作为 Azure Stack 的服务进行故障排除。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 4372da2f1057c06761dd6abaf18c26df6e33640e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235213"
---
# <a name="troubleshoot-validation-as-a-service"></a>对验证作为服务进行故障排除

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

以下是常见的问题不相关的软件版本，以及其解决方案。

## <a name="the-portal-shows-local-agent-in-debug-mode"></a>门户会显示在调试模式下的本地代理

这可能是因为代理将无法向服务发送的检测信号，由于不稳定的网络连接。 检测信号发送每隔五分钟。 如果服务没有为 15 分钟内收到检测信号，则服务认为代理处于非活动状态，并测试将无法再在它上面安排。 检查错误消息*Agenthost.log*文件位于目录中，代理已启动。

> [!Note] 
> 已在代理上运行任何测试将继续运行，但如果检测信号不还原后，才能在测试结束，然后，代理将无法更新测试状态或将日志上传。 测试将始终显示为**运行**并且将需要取消。

## <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>执行测试时，在计算机上的代理进程已关闭。 要发生的情况

如果代理进程关闭的情况下以非正常方式为例，计算机重新启动，已终止进程 （代理窗口上的 CTRL + C 被视为正常关闭） 则在其运行的测试将继续显示为**运行**。 如果重新启动代理，则代理将更新到测试的状态**取消**。 如果不重新启动代理，则该测试显示为**运行**并且您必须手动取消测试

> [!Note] 
> 工作流中的测试计划以按顺序运行。 **挂起**未获取直到中的测试执行测试**运行**状态处于相同的工作流完成。

## <a name="handle-slow-network-connectivity"></a>处理慢速网络连接

在本地数据中心，可以下载到共享 PIR 映像。 然后你可以验证该映像。

<!-- This is from the appendix to the Deploy local agent topic. -->

### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>PIR 映像下载到本地共享时速度缓慢的网络流量

1. 下载 AzCopy 从： [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. 提取 AzCopy.zip 并将更改为包含 AzCopy.exe 的目录

3. 从提升的提示符打开 Windows PowerShell。 运行以下命令：

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare 是共享路径或本地路径。

### <a name="verifying-pir-image-file-hash-value"></a>验证 PIR 图像文件哈希值

可以使用**Get-HashFile** cmdlet 来获取下载公共映像存储库图像文件，若要查看的映像完整性的哈希值。

| 文件名 | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604 20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

## <a name="next-steps"></a>后续步骤

- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。
