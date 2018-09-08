---
title: 部署本地代理和测试映像的虚拟机作为服务的 Azure Stack 验证 |Microsoft Docs
description: 为 Azure Stack 验证即服务部署本地代理并测试映像虚拟机。
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
ms.openlocfilehash: 78136ab00dcba2f8a99df36ba99d384b49995882
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159208"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>部署本地代理并测试虚拟机

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

了解如何使用验证即服务 (VaaS) 本地代理来检查硬件。 在运行验证测试之前，必须在 Azure Stack 解决方案上部署本地代理。

> [!Note]  
> 必须确保运行本地代理的计算机不会无法访问 Internet。 计算机只能供有权使用 Azure Stack VaaS 的用户访问。

若要测试虚拟机，请执行以下操作：

1. 安装本地代理
2. 将故障注入系统
3. 运行本地代理

## <a name="download-and-start-the-local-agent"></a>下载并启动本地代理

将代理下载到数据中心的符合先决条件的计算机，该数据中心不是 Azure Stack 系统的一部分，但是能够访问所有 Azure Stack 终结点。

### <a name="machine-prerequisites"></a>计算机先决条件

检查计算机是否符合以下条件：

- 能够访问所有 Azure Stack 终结点
- 已安装 .NET 4.6 和 PowerShell 5.0
- 至少 8 GB 的 RAM
- 至少为 8 核处理器
- 至少 200 GB 的磁盘空间
- 能够稳定地连接到 Internet

Azure Stack 是接受测试的系统。 计算机不应是 Azure Stack 的一部分，也不应托管在 Azure Stack 云中。

### <a name="download-and-install-the-agent"></a>下载并安装代理

1. 在将要用来运行测试的计算机的提升权限的提示符窗口中打开 Windows PowerShell。
2. 运行以下命令来下载本地代理：

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. 运行以下命令安装本地代理依赖项：

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
    | aadServiceAdminUser | Azure AD 租户的全局管理员用户。 例如，它可以是 vaasadmin@contoso.onmicrosoft.com。 |
    | aadServiceAdminPassword | 全局管理员用户的密码。 |
    | AadTenantId | 注册到验证即服务的 Azure 帐户的 Azure AD 租户 ID。 |
    | ExternalFqdn | 可以从配置文件获取完全限定的域名。 有关说明，请参阅[验证即服务 Azure Stack 的测试参数](azure-stack-vaas-parameters-test.md)。 |
    | 区域 | Azure AD 租户的区域。 |

此命令下载公共映像存储库 (PIR) 映像 (OS VHD) 并将其从 Azure Blob 存储复制到 Azure Stack 存储。 

![下载必备组件](media/installingprereqs.png)

> [!Note]  
> 如果在下载这些映像时遇到网络速度过慢的问题，请将其分开下载到本地共享并指定参数 **-LocalPackagePath** *FileShareOrLocalPath*。 有关 PIR 下载的更多指南，请参阅[对验证即服务进行故障排除](azure-stack-vaas-troubleshoot.md)的[处理网络连接过慢的问题](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)部分。

## <a name="fault-injection"></a>故障注入

Microsoft 设计 Azure Stack 的复原能力并允许多个类型的软件和硬件故障。 故障注入可以提高系统的故障率。 这样有助于尽早发现问题，减少会导致系统故障的事件数。

运行以下命令，将故障注入系统。

1. 在提升权限的提示符窗口中打开 Windows PowerShell。

2. 运行以下命令：

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>运行代理

1. 在提升权限的提示符窗口中打开 Windows PowerShell。

2. 运行以下命令：

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **参数**  
    
    | 参数 | 说明 |
    | --- | --- |
    | VaaSUserId | 用于登录到 VaaS 门户的用户 ID（例如 UserName@Contoso.com） |
    | VaaSTenantId | 注册到验证即服务的 Azure 帐户的 Azure AD 租户 ID。 |

    > [!Note]  
    > 运行代理时，当前工作目录必须是任务引擎主机可执行文件 **Microsoft.VaaSOnPrem.TaskEngineHost.exe** 的位置。

如果没有看到系统报告任何错误，则本地代理已成功。 以下示例文本显示在控制台窗口中。

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![启动的代理](media/startedagent.png)

代理可以通过名称进行唯一标识。 默认情况下，它使用启动时所在计算机的完全限定的域名 (FQDN)。 若要避免在窗口中意外单击，必须将窗口最小化，因为更改焦点会暂停所有其他操作。

## <a name="next-steps"></a>后续步骤

- [验证新的 Azure Stack 解决方案](azure-stack-vaas-validate-solution-new.md)  
- 如果 Internet 连接过慢或时断时续，可以下载 PIR 映像。 有关详细信息，请参阅[处理网络连接过慢的问题](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity)。
- 详细了解 [Azure Stack 验证即服务](https://docs.microsoft.com/azure/azure-stack/partner)。
