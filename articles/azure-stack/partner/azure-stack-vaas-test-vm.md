---
title: 部署本地代理和测试映像的虚拟机作为服务的 Azure Stack 验证 |Microsoft Docs
description: 部署本地代理和测试映像虚拟机作为服务的 Azure Stack 验证。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 9d32c23f66563988d023df3bf6a33efa30237e57
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235281"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>部署本地代理和测试虚拟机

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何使用服务 (VaaS) 本地代理作为验证检查您的硬件。 本地代理必须运行验证测试之前正在验证 Azure Stack 解决方案上进行部署。

> [!Note]  
> 必须确保运行本地代理，计算机不会失去对 Internet 的访问权限。 机仅必须可供有权使用 Azure Stack VaaS 的用户访问。

若要测试你的虚拟机：

1. 安装本地代理
2. 将故障注入到你的系统
3. 运行本地代理

## <a name="download-and-start-the-local-agent"></a>下载并启动本地代理

将代理下载到满足你不是 Azure Stack 系统中，但一个有权访问所有 Azure Stack 终结点的一部分的数据中心中的先决条件的计算机。

### <a name="machine-prerequisites"></a>计算机先决条件

检查你的计算机满足以下条件：

- 访问所有 Azure Stack 终结点
- .NET 4.6 和安装 PowerShell 5.0
- 至少 8 GB RAM
- 最小值 8 核处理器
- 最小 200 GB 磁盘空间
- 稳定的网络连接到 internet

Azure Stack 是待测试系统。 计算机不应属于 Azure Stack 或 Azure Stack 云中托管。

### <a name="download-and-install-the-agent"></a>下载并安装代理

1. 在将用于运行测试的计算机上提升的提示符打开 Windows PowerShell。
2. 运行以下命令以下载本地代理：

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. 运行以下命令以安装本地代理的依赖项：

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **参数**

    | 参数 | 说明 |
    | --- | --- |
    | aadServiceAdminUser | Azure AD 租户全局管理员用户。 例如它可能是， vaasadmin@contoso.onmicrosoft.com。 |
    | aadServiceAdminPassword | 全局管理员用户的密码。 |
    | AadTenantId | Azure 帐户的 azure AD 租户 ID 注册为服务的验证。 |
    | ExternalFqdn | 可以从配置文件获取完全限定的域名。 有关说明，请参阅[测试验证作为 Azure Stack 的服务的参数](azure-stack-vaas-parameters-test.md)。 |
    | 区域 | Azure AD 租户的区域。 |

命令从 Azure blob 存储下载一个公共映像存储库 (PIR) 映像 (OS VHD) 并复制到 Azure Stack 存储。 

![下载系统必备组件](media/installingprereqs.png)

> [!Note]  
> 如果您遇到慢速网络速度下载这些映像时，请单独下载到本地共享，并指定参数 **-LocalPackagePath** *FileShareOrLocalPath*。 可以在部分 PIR 下载上找到更多指导[句柄慢速网络连接](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)的[验证作为服务进行故障排除](azure-stack-vaas-troubleshoot.md)。

## <a name="fault-injection"></a>错误注入

Microsoft 设计 Azure Stack 的复原能力并允许多个类型的软件和硬件故障。 错误注入系统中增加的错误的频率。 这种提高可帮助你发现问题之前，以便可以减少使系统停机的事件数。

运行以下命令以将故障注入到你的系统。

1. 在提升的提示符打开 Windows PowerShell。

2. 运行以下命令：

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>运行代理

1. 在提升的提示符打开 Windows PowerShell。

2. 运行以下命令：

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **参数**  
    
    | 参数 | 说明 |
    | --- | --- |
    | VaaSUserId | 使用用户 ID 登录到 VaaS 门户 (例如， UserName@Contoso.com) |
    | VaaSTenantId | Azure 帐户的 azure AD 租户 ID 注册为服务的验证。 |

    > [!Note]  
    > 当前工作目录运行代理时，必须为任务引擎宿主可执行文件的位置**Microsoft.VaaSOnPrem.TaskEngineHost.exe。**

如果看不到报告任何错误，然后本地代理已成功完成。 下面的示例文本将显示在控制台窗口。

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![启动的代理](media/startedagent.png)

其名称唯一标识代理。 默认情况下，它使用起始位置的计算机的完全限定的域名 (fqdn) 名称。 必须最小化窗口，以避免对窗口中，根据任何意外的单击更改焦点暂停所有其他操作。

## <a name="next-steps"></a>后续步骤

- [验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)  
- 如果您具有慢速或断续 Internet 连接，可以下载 PIR 映像。 有关详细信息，请参阅[句柄慢速网络连接](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)。
- 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。
