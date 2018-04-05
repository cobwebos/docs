---
title: Azure 日志集成入门 | Microsoft Docs
description: 了解如何安装 Azure 日志集成服务并集成来自 Azure 存储、Azure 审核日志和 Azure 安全中心警报的日志。
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/20/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 3e229c4db44fc3c8d16aa2bd0a014fb1acc64a5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>将 Azure 日志集成与 Azure 诊断日志记录和 Windows 事件转发相结合

如果无法从安全事故和事件管理 (SIEM) 供应商获取 [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) 连接器，客户可以改用 Azure 日志集成。 Azure 日志集成可以将 Azure 日志提供给 SIEM，并可以用来创建适用于所有资产的统一仪表板。

> [!NOTE]
> 有关 Azure Monitor 的详细信息，请参阅 [Azure Monitor 入门](../monitoring-and-diagnostics/monitoring-get-started.md)。 有关 Azure Monitor 连接器状态的详细信息，请与 SIEM 供应商联系。

> [!IMPORTANT]
> 如果你的主要兴趣是收集虚拟机日志，则大多数 SIEM 供应商在其解决方案中都包括了此选项。 使用 SIEM 供应商的连接器始终是首选替代方法。

本文可帮助你学习 Azure 日志集成的入门知识。 其中重点介绍了如何安装 Azure 日志集成服务，以及如何将该服务与 Azure 诊断集成。 然后，Azure 日志集成服务就可以从 Azure 基础结构即服务中部署的虚拟机，通过 Windows 安全事件通道收集 Windows 事件日志信息。 这类似于在本地系统中使用的“事件转发”。

> [!NOTE]
> SIEM 自身能够将 Azure 日志集成的输出与 SIEM 集成。 有关详细信息，请参阅[将 Azure 日志集成与本地 SIEM 集成](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)。

Azure 日志集成服务在运行 Windows Server 2008 R2 或更高版本（最好是 Windows Server 2016 或 Windows Server 2012 R2）的物理计算机或虚拟计算机上运行。

物理计算机可以在本地或托管站点上运行。 如果选择在虚拟机上运行 Azure 日志集成服务，则该虚拟机可以位于本地或公有云中，例如 Microsoft Azure。

运行 Azure 日志集成服务的实体计算机或虚拟计算机需要通过网络连接到 Azure 公有云。 本文提供有关所需配置的详细信息。

## <a name="prerequisites"></a>先决条件

安装 Azure 日志集成至少需要以下各项：

* 一个 **Azure 订阅**。 如果没有，可以注册 [免费帐户](https://azure.microsoft.com/free/)。
* 一个**存储帐户**，可以用于 Windows Azure 诊断日志记录 (WAD)。 可以使用预配置的存储帐户，也可以创建新的存储帐户。 本文稍后将介绍如何配置存储帐户。

  > [!NOTE]
  > 根据具体的场景，可能无需存储帐户。 本文中所述的 Azure 诊断场景需要存储帐户。

* **两个系统**： 
  * 运行 Azure 日志集成服务的计算机。 这台计算机收集所有的日志信息，些日志信息随后会被导入到 SIEM 中。 此系统：
    * 可以位于本地，也可以托管在 Microsoft Azure 中。  
    * 必须在 x64 版本的 Windows server 2008 R2 SP1 或更高版本中运行，并且装有 Microsoft .NET 4.5.1。 若要确定所安装的 .NET 版本，请参阅[确定安装的 .NET Framework 版本](https://msdn.microsoft.com/library/hh925568)。  
    * 必须连接到 Azure 诊断日志记录所使用的 Azure 存储帐户。 本文稍后将介绍如何确认连接。
  * 要监视的计算机。 这是作为 [Azure 虚拟机](../virtual-machines/virtual-machines-windows-overview.md)运行的 VM。 来自此计算机的日志记录信息将发送到 Azure 日志集成服务计算机。

有关使用 Azure 门户创建虚拟机的快速演示，请观看以下视频：<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>部署注意事项

在测试期间，可以使用任何系统，只要它满足最低操作系统要求即可。 在生产环境中，可能需要对负载规划纵向或横向扩展。

可以运行 Azure 日志集成服务的多个实例， 但是，在每台物理机或虚拟机上，只能运行该服务的一个实例。 另外，可对 WAD 的 Azure 诊断存储帐户进行负载均衡。 提供给实例的订阅数根据容量而定。

> [!NOTE]
> 目前，我们不针对应该在什么时候扩展 Azure 日志集成计算机（即运行 Azure 日志集成服务的计算机）的实例做出具体建议，也不针对存储帐户或订阅做具体建议。 请根据上述每个方面的性能观察结果做出缩放决定。

若要帮助提高性能，可以选择纵向扩展 Azure 日志集成服务。 以下性能指标有助于确定运行 Azure 日志集成服务所使用的计算机的大小：

* 在一台 8 处理器（核心）的计算机上，Azure 日志集成的单个实例每天可以处理大约 2400 万个事件（每小时处理大约 100 万个事件）。
* 在一台 ４ 处理器（核心）的计算机上，Azure 日志集成的单个实例每天可以处理大约 150 万个事件（每小时处理大约 6.25 万个事件）。

## <a name="install-azure-log-integration"></a>安装 Azure 日志集成

若要安装 Azure 日志集成，请下载 [Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324)安装文件。 完成安装过程。 选择是否向 Microsoft 提供遥测信息。

Azure 日志集成服务会收集安装了该服务的计算机中的遥测数据。  

收集的遥测数据包括：

* 在 Azure 日志集成执行过程中发生的异常。
* 关于已处理的查询数量和事件数量的指标。
* 关于所用 Azlog.exe 命令行选项的统计信息。 

> [!NOTE]
> 建议允许 Microsoft 收集遥测数据。 清除“允许 Microsoft 收集遥测数据”复选框可以关闭遥测数据的收集。
>

![安装窗格的屏幕截图，其中的遥测复选框已选中](./media/security-azure-log-integration-get-started/telemetry.png)


以下视频介绍了安装过程：<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>安装后和验证步骤

完成基本安装后，可以执行以下安装后和验证步骤：

1. 以管理员身份打开 PowerShell。 然后导航到 C:\Program Files\Microsoft Azure Log Integration。
2. 导入 Azure 日志集成 cmdlet。 若要导入 cmdlet，请运行脚本 `LoadAzlogModule.ps1`。 输入 `.\LoadAzlogModule.ps1` 并按 Enter（请注意，此命令中使用了 **.\\**）。 应该会看到如下图所示的内容：

  ![LoadAzlogModule.ps1 命令的输出屏幕截图](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. 接下来，将 Azure 日志集成配置为使用特定的 Azure 环境。 “Azure 环境”是要使用的 Azure 云数据中心的类型。 虽然有多个可用的 Azure 环境，但目前最相关的选项是 **AzureCloud** 或者 **AzureUSGovernment**。 以管理员身份运行 PowerShell，确保在 C:\program files\Microsoft Azure Log Integration\ 中操作。 然后运行以下命令：

  `Set-AzlogAzureEnvironment -Name AzureCloud`（适用于 **AzureCloud**）
  
  若要使用美国政府版 Azure 云，请在 **-Name** 变量中使用 **AzureUSGovernment**。 目前不支持其他 Azure 云。  

  > [!NOTE]
  > 如果命令成功，则不会受到任何反馈。 

4. 监视系统之前，需要用于 Azure 诊断的存储帐户的名称。 在 Azure 门户中，转到“虚拟机”。 找到要监视的虚拟机。 在“属性”部分，选择“诊断设置”。  然后选择“代理”。 记下指定的存储帐户名称。 后面的步骤中需要此帐户名称。

  ![Azure 诊断设置窗格的屏幕截图](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![“启用来宾级监视”按钮的屏幕截图](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > 如果在创建虚拟机时未启用监视，可按上图所示启用监视。

5. 现在，请返回到 Azure 日志集成计算机。 验证已安装 Azure 日志集成的系统是否连接到了存储帐户。 运行 Azure 日志集成服务的计算机需要访问存储帐户，以检索 Azure 诊断在每台受监视系统上记录的信息。 验证连接： 
  1. [下载 Azure 存储资源管理器](http://storageexplorer.com/)。
  2. 完成安装。
  3. 完成安装后，选择“下一步”。 将“启动 Microsoft Azure 存储资源管理器”复选框保留选中状态。  
  4. 登录 Azure。
  5. 验证是否可以看到为 Azure 诊断配置的存储帐户： 

    ![存储资源管理器中的存储帐户屏幕截图](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. 存储帐户下面有几个选项。 在“表”下，应该可以看到名为 **WADWindowsEventLogsTable** 的表。

  如果在创建虚拟机时未启用监视，可根据前面所述启用监视。


## <a name="integrate-azure-diagnostics-logging"></a>集成 Azure 诊断日志记录

此步骤将运行 Azure 日志集成服务的计算机配置为连接到包含日志文件的存储帐户。

若要完成此步骤，首先做好以下准备：  
* **FriendlyNameForSource**：一个友好名称，可将其应用到为虚拟机配置的存储帐户，以存储来自 Azure 诊断的信息。
* **StorageAccountName**：配置 Azure 诊断时指定的存储帐户的名称。  
* **StorageKey**：为此虚拟机存储 Azure 诊断信息的存储帐户的存储密钥。  

若要获取存储密钥，请完成以下步骤：
1. 转到 [Azure 门户](http://portal.azure.com)。
2. 在导航窗格中，选择“所有服务”。
3. 在“筛选器”框中，输入“存储”。 然后选择“存储帐户”。

  ![显示“所有服务”中的存储帐户的屏幕截图](./media/security-azure-log-integration-get-started/filter.png)

4. 此时会显示存储帐户的列表。 双击分配给日志存储的帐户。

  ![显示存储帐户列表的屏幕截图](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. 在“设置”下，选择“访问密钥”。

  ![显示菜单中“访问密钥”选项的屏幕截图](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. 复制“密钥 1”，并将其保存在下一步骤可以访问的安全位置。
7. 在安装了 Azure 日志集成的服务器上，以管理员身份打开命令提示符窗口。 （请务必以管理员身份打开命令提示符窗口，而不要打开 PowerShell）。
8. 转到 C:\Program Files\Microsoft Azure Log Integration。
9. 运行以下命令：`Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`。
 
  示例：
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  若要在事件 XML 中显示订阅 ID，请将订阅 ID 追加到友好名称中：

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  示例： 
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> 最多等待 60 分钟，然后查看从存储帐户提取的事件。 若要查看事件，请在 Azure 日志集成中选择“事件查看器” > “Windows 日志” > “已转发事件”。

以下视频介绍了上述步骤：<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>如果数据未显示在“已转发事件”文件夹中
如果在 1 小时后数据未显示在“已转发事件”文件夹中，请完成以下步骤：

1. 检查运行 Azure 日志集成服务的计算机。 确认它可以访问 Azure。 若要测试连接，请尝试在浏览器中转到 [Azure 门户](http://portal.azure.com)。
2. 请确保用户帐户 Azlog 对文件夹 users\Azlog 拥有写入权限。
  1. 打开文件资源管理器。
  2. 转到 C:\users。
  3. 右键单击“C:\users\Azlog”。
  4. 选择“安全性”。
  5. 选择“NT Service\Azlog”。 检查帐户权限。 如果此选项卡中没有帐户，或者未显示相应的权限，可以在此选项卡中授予帐户权限。
3. 运行命令 `Azlog source list` 时，请确保输出中列出了在命令 `Azlog source add` 中添加的存储帐户。
4. 若要查看 Azure 日志集成服务是否报告了任何错误，请转到“事件查看器” > “Windows 日志” > “应用程序”。

如果在安装和配置期间遇到任何问题，请创建[支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 对于服务，请选择“日志集成”。

另一个支持选项是 [Azure 日志集成 MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration)。 在 MSDN 论坛中，社区可以解答有关如何充分利用 Azure 日志集成的问题并分享提示和技巧，以此提供支持。 此外，Azure 日志集成团队也会关注此论坛， 并尽可能地提供帮助。

## <a name="next-steps"></a>后续步骤
若要详细了解 Azure 日志集成，请参阅以下文章：

* [适用于 Azure 日志的 Microsoft Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324)。 下载中心提供有关 Azure 日志集成的详细信息、系统要求和安装说明。
* [Azure 日志集成简介](security-azure-log-integration-overview.md)。 此文介绍 Azure 日志集成、其主要功能和工作原理。
* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)。 此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。 它是有关如何配置 SIEM 组件的最新指导。 有关其他详细信息，请咨询 SIEM 供应商。
* [Azure 日志集成常见问题 (FAQ)](security-azure-log-integration-faq.md)。 此文提供有关 Azure 日志集成的常见问题的解答。
* [使用 Azure 日志集成来集成 Azure 安全中心警报](../security-center/security-center-integrating-alerts-with-log-integration.md)。 此文介绍如何同步安全中心警报以及由 Azure 诊断和 Azure 活动日志收集的虚拟机安全事件。 可以使用 Azure Log Analytics 或 SIEM 解决方案来同步日志。
* [Azure 诊断和 Azure 审核日志的新功能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/)。 此博客文章介绍 Azure 审核日志和其他功能，可帮助你深入了解 Azure 资源的操作。
