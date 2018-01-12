---
title: "在 Azure 堆栈中使用的特权的终结点 |Microsoft 文档"
description: "演示如何在 Azure 堆栈 (for Azure 堆栈运算符） 中使用的特权的终结点。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 80c3f248edb40b66e3177c512f3caf77295c6c5d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>在 Azure 堆栈中使用的特权的终结点

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

作为 Azure 堆栈操作员，应使用管理员门户中，PowerShell 或 Azure 资源管理器 Api，用于最日常管理任务。 但是，对于一些不太常见的操作，你需要使用*特权终结点*(PEP)。 此终结点是预配置的远程 PowerShell 控制台，你提供刚好够用的功能，可帮助你执行所需的任务。 终结点利用 PowerShell JEA (Just Enough Administration) 来公开仅一组受限的 cmdlet。 若要访问特权终结点，并调用受限制的一组 cmdlet，请使用低特权帐户。 没有管理员帐户是必需的。 为了加强安全，不允许脚本。

特权的终结点可用于执行诸如下列任务：

- 执行低级任务，如[收集诊断日志](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool)。
- 对于集成系统，例如部署后，设置图形集成，Active Directory 联合身份验证服务 (AD FS) 集成，证书添加域名系统 (DNS) 转发器执行许多后期部署数据中心的集成任务旋转，等等。
- 若要支持以获取对深度故障排除集成的系统的临时的高级访问权限。 

特权的终结点记录每个操作 （和其相应的输出） 在 PowerShell 会话中执行。 这提供了完全透明和完成审核的操作。 你可以保留供将来审核使用这些日志文件。

> [!NOTE]
> 在 Azure 堆栈开发工具包 (ASDK)，你可以某些特权终结点中可用命令直接从 PowerShell 会话在主机上运行开发工具包。 但是，你可能想要测试使用特权终结点的日志收集，例如，某些操作，因为这是可用于在集成的系统环境中执行某些操作的唯一方法。

## <a name="access-the-privileged-endpoint"></a>访问特权终结点

通过托管特权终结点的虚拟机上的远程 PowerShell 会话访问特权终结点。 ASDK，在此虚拟机是名为 AzS ERCS01。 如果你使用的集成的系统，有三个实例的特权终结点，每个运行在虚拟机 (*前缀*-ERCS01，*前缀*-ERCS02，或*前缀*-ERCS03) 以灵活的不同主机上。 

在开始一个集成系统，此过程之前，请确保你可以通过 IP 地址，或通过 DNS 访问特权的终结点。 之后的 Azure 堆栈的初始部署，你可以访问特权终结点只能通过 IP 地址来因为 DNS 集成未尚未设置。 OEM 硬件供应商提供一个名为"AzureStackStampDeploymentInfo"包含特权的终结点 IP 地址的 JSON 文件。

我们建议你连接到特权终结点只能从硬件生命周期主机或从一台专用、 锁定计算机，如[特权访问工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)。

1. 执行具体取决于你的环境以下任一操作：

    - 在集成的系统中，运行以下命令以将特权终结点添加为你的硬件生命周期主机或特权访问工作站上受信任的主机。

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - 如果你正在运行 ADSK，登录到开发工具包主机。

2. 在你的硬件生命周期主机或特权访问工作站，打开提升的 Windows PowerShell 会话。 运行以下命令以建立虚拟机承载特权终结点上的远程会话：
 
    - 在集成系统上：
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName`参数可以是 IP 地址或其中一个虚拟机承载特权的终结点的 DNS 名称。 
    - 如果你正在运行 ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   出现提示时，使用以下凭据：

      - **用户名称**: CloudAdmin 帐户中，指定格式 **&lt;* Azure 堆栈域*&gt;\cloudadmin**。 (对于 ASDK，用户名称是**azurestack\cloudadmin**。)
      - **密码**： 输入 AzureStackAdmin 域管理员帐户安装期间提供的相同密码。
    
3.  连接后，提示将更改为 **[*IP 地址或 ERCS VM 名称*]: PS > * * 或**[azs ercs01]: PS >**，取决于环境。 从此处，运行`Get-Command`若要查看可用的 cmdlet 的列表。

    ![Get-command cmdlet 的可用命令的输出显示列表](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    其中许多 cmdlet 旨在仅供 （例如与数据中心集成相关的 cmdlet) 的集成的系统环境变量。 在 ASDK，验证以下 cmdlet:

    - 清除主机
    - 关闭 PrivilegedEndpoint
    - Exit-pssession
    - Get AzureStackLog
    - Get AzureStackStampInformation
    - Get 命令
    - Get-formatdata
    - Get-Help
    - Get ThirdPartyNotices
    - 度量值对象
    - 新 CloudAdminUser
    - Out-default
    - 删除 CloudAdminUser
    - 选择对象
    - 集 CloudAdminUserPassword
    - 测试 AzureStack
    - 停止 AzureStack
    - Get-clusterlog

4.  不允许脚本，因为不能为参数值中使用 tab 自动补全。 若要获取给定 cmdlet 的参数的列表，请运行以下命令：

    ````PowerShell
    Get-Command <cmdlet_name> -Syntax
    ```` 
    如果你尝试任何类型的脚本操作，该操作失败，出现错误**ScriptsNotAllowed**。 这是预期的行为。

## <a name="close-the-privileged-endpoint-session"></a>关闭特权终结点会话

 如前所述，特权终结点将记录每个操作 （和其相应的输出） 在 PowerShell 会话中执行。 您应使用来关闭会话`Close-PrivilegedEndpoint`cmdlet。 此 cmdlet 正确关闭终结点，并将日志文件传输到保留的外部文件共享。

若要关闭终结点会话：

1. 创建外部文件共享访问的特权的终结点。 在开发工具包环境中，你可以只是文件共享主机上创建开发工具包。
2. 运行`Close-PrivilegedEndpoint`cmdlet。 
3. 系统提示输入上用于存储脚本日志文件的路径。 指定更早版本，格式 &#92; &#92; 中创建的文件共享*servername*&#92;*sharename*。 如果不指定路径，该 cmdlet 失败并且会话保持打开状态。 

    ![关闭 PrivilegedEndpoint cmdlet 输出中显示你在其中指定脚本的目标路径](media/azure-stack-privileged-endpoint/closeendpoint.png)

脚本日志文件已成功传输到文件共享后，它们是自动从特权终结点中删除。 如果你关闭特权终结点会话使用的 cmdlet`Exit-PSSession`或`Exit`，或只需关闭 PowerShell 控制台，脚本日志不传输到的文件共享。 日志保留在特权终结点。 下次运行`Close-PrivilegedEndpoint`和包含文件共享，从上一个脚本日志还将传输会话。

## <a name="next-steps"></a>后续步骤
[Azure 堆栈诊断工具](azure-stack-diagnostics.md)







