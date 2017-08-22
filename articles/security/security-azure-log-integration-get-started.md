---
title: "Azure 日志集成入门 | Microsoft Docs"
description: "了解如何安装 Azure 日志集成服务并集成来自 Azure 存储、Azure 审核日志和 Azure 安全中心警报的日志。"
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
ms.date: 07/26/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: a5c51817688140cc2778602b4c1d5184ae4729a0
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>将 Azure 日志集成与 Azure 诊断日志记录和 Windows 事件转发相结合
Azure 日志集成 (AzLog) 使你能够将 Azure 资源中的原始日志集成到本地安全信息和事件管理 (SIEM) 系统。 此集成可以让本地或云中的所有资产使用统一的仪表板，以便聚合、相互关联、分析和警示与应用程序相关的安全事件。
>[!NOTE]
有关 Azure 日志集成的详细信息，请查阅 [Azure 日志集成概述](https://docs.microsoft.com/azure/security/security-azure-log-integration-overview)。

本文将重点介绍如何安装 Azlog 服务和如何将服务与 Azure 诊断集成，从而帮助你开始使用 Azure 日志集成。 然后，Azure 日志集成服务就可以从部署在 Azure IaaS 中的虚拟机上通过 Windows 安全事件通道收集 Windows 事件日志信息。 可能已经在本地使用了“事件转发”，这二者非常类似。

>[!NOTE]
>SIEM 自身能够将 Azure 日志集成的输出集成到 SIEM。 有关详细信息，请参阅文章[将 Azure 日志集成与本地 SIEM 集成](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)。

请注意 - 运行 Azure 日志集成服务的实体计算机或虚拟计算机必须使用 Windows Server 2008 R2 或更高版本的操作系统（最好使用 Windows Server 2012 R2 或 Windows Server 2016）。

实体计算机可以在本地（或主机托管服务提供商网站上）运行。 如果选择在虚拟机上运行 Azure 日志集成服务，则该虚拟机可以位于本地或公有云中，例如 Microsoft Azure。

运行 Azure 日志集成服务的实体计算机或虚拟计算机需要通过网络连接到 Azure 公有云。 本文中的步骤介绍了有关配置的详细信息。

## <a name="prerequisites"></a>先决条件
安装 AzLog 至少需要以下各项：
* 一个 **Azure 订阅**。 如果没有帐户，可以注册一个[免费帐户](https://azure.microsoft.com/free/)。
* 用于 Windows Azure 诊断日志记录的**存储帐户**（可以使用预先配置的存储帐户，也可以新建一个帐户 - 本文稍后将演示如何配置存储帐户）
  >[!NOTE]
  可能无需存储帐户，具体取决于你的方案。 对于本文中所述的 Azure 诊断方案，需要一个存储帐户。
* **两个系统**：一个计算机运行 Azure 日志集成服务，一个计算机被监视并将其日志记录信息发送给运行 Azlog 服务的计算机。
   * 要监视的计算机 – 这台计算机是作为 [Azure 虚拟机](../virtual-machines/virtual-machines-windows-overview.md)运行的 VM
   * 运行 Azure 日志集成服务的计算机；这台计算机会收集所有的日志信息，而这些日志信息随后会被导入到 SIEM 中。
    * 此系统可以位于本地也可以位于 Microsoft Azure 中。  
    * 需要在 64 位版本的 Windows server 2008 R2 SP1 或更高版本中运行，并且需要安装 .NET 4.5.1。 可以按照标题为[如何：确定所安装的 .NET Framework 版本](https://msdn.microsoft.com/library/hh925568)的文章进行操作，以确定所安装的 .NET 版本  
    必须连接到 Azure 诊断日志记录所使用的 Azure 存储帐户。 本文稍后介绍如何确认此连接

有关使用 Azure 门户创建虚拟机的过程的快速演示，请观看以下视频。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]



## <a name="deployment-considerations"></a>部署注意事项
测试 Azure 日志集成时，可以使用任何系统，只要它满足最低操作系统要求。 但是在生产环境中，负载可能需要你计划缩放。

当事件量过高时，可以运行 Azure 日志集成服务的多个实例（一个实例对应一台实体或虚拟计算机）。 此外，还可以为 Windows (WAD) 加载均衡的 Azure 诊断存储帐户，并且根据容量确定用于提供给实例的订阅数。
>[!NOTE]
此处我们不针对应该在什么时候扩展 Azure 日志集成计算机（即运行 Azure 日志集成服务的计算机）的实例做出具体建议，也不针对存储帐户或订阅做具体建议。 应该根据上述每个方面的性能观察结果做出缩放决定。

还可以通过扩展 Azure 日志集成服务来提高性能。 以下性能指标有助于确定运行 Azure 日志集成服务所使用的计算机的大小：
* 在一台 8 处理器（内核）的计算机上，Azlog 集成器的单个实例每天可以处理大约 2400 万个事件（每小时处理大约 100 万个事件）。

* 在一台 ４ 处理器（内核）的计算机上，Azlog 集成器的单个实例每天可以处理大约 150 万个事件（每小时处理大约 6.25 万个事件）。

## <a name="install-azure-log-integration"></a>安装 Azure 日志集成
若要安装 Azure 日志集成，需要下载 [Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324)安装文件。 执行安装例程，并决定是否向 Microsoft 发送遥测数据。  

![选中了遥测数据框的安装屏幕](./media/security-azure-log-integration-get-started/telemetry.png)

*
> [!NOTE]
> 建议允许 Microsoft 收集遥测数据。 通过取消选中此选项，可以关闭遥测数据的收集。
>


Azure 日志集成服务会收集安装了该服务的计算机中的遥测数据。  

收集的遥测数据包括：

* 在 Azure 日志集成执行过程中发生的异常
* 关于已处理的查询数量和事件数量的指标
* 关于正在使用哪些 Azlog.exe 命令行选项的统计信息

以下视频介绍了安装过程。
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>安装后和验证步骤
完成了基本的安装例程之后，就可以执行以下安装后和验证步骤了：
1. 打开提升权限的 PowerShell 窗口并导航到 **c:\Program Files\Microsoft Azure Log Integration**
2. 首先需要导入 AzLog Cmdlet。 可以通过运行 **LoadAzlogModule.ps1** 脚本来实现此目的（请注意以下命令中的“\”）。 输入 **.\LoadAzlogModule.ps1** 并按 **Enter** 。  
应该会看到如下图所示的内容。 </br></br>
![选中了遥测数据框的安装屏幕](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. 现在需要将 AzLog 配置为使用特定的 Azure 环境。 “Azure 环境”即想使用的 Azure 云数据中心的“类型”。 虽然此时有多个 Azure 环境，但是当前最相关的选项是 **AzureCloud** 或者 **AzureUSGovernment**。   在提升权限的 PowerShell 环境中，确保位于 **c:\program files\Microsoft Azure Log Integration\** </br></br>
    位于此处后，运行以下命令： </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud``（适用于 Azure 商业版）

      >[!NOTE]
      如果命令成功，则不会受到任何反馈。  如果想使用美国政府 Azure 云，请使用适用于美国政府云的 **AzureUSGovernment**（用于 -Name 变量）。 此时不支持其他 Azure 云。  
4. 监视系统之前，需要 Azure 诊断使用的存储帐户的名称。  在 Azure 门户中，导航到“虚拟机”并找到想监视的虚拟机。 在“属性”部分，选择“诊断设置”。  单击“代理”并记下指定的存储帐户名称。 后面的步骤中需要此帐户名称。
![Azure 诊断设置](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

      ![Azure 诊断设置](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)
      >[!NOTE]
      如果在创建虚拟机时未启用监视，会提供启用监视的选项，如上所示。
5. 现在我们重新将注意力放回 Azure 日志集成计算机。 我们需要验证已安装 Azure 日志集成的系统是否连接到了存储帐户。 运行 Azure 日志集成服务的实体计算机或虚拟计算机需要访问存储帐户，以便按每台被监视系统上的配置检索由 Azure 诊断记录的信息。  
  1. 可在[此处](http://storageexplorer.com/)下载 Azure 存储资源管理器。
  2. 执行安装例程
  3. 完成安装后，单击“下一步”，并让“启动 Microsoft Azure 存储资源管理器”复选框保持选中状态。  
  4. 登录 Azure。
  5. 验证是否可以看到为 Azure 诊断配置的存储帐户。  
![存储帐户](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
   6. 请注意，在存储帐户下有几个选项。 其中一个选项是“表”。 在“表”下，应该可以看到“WADWindowsEventLogsTable”选项。 </br></br>
   ![存储帐户](./media/security-azure-log-integration-get-started/storage-explorer.png) </br>

## <a name="integrate-azure-diagnostic-logging"></a>集成 Azure 诊断日志记录
在此步骤中，会将运行 Azure 日志集成服务的计算机配置为连接到包含日志文件的存储帐户。
为完成此步骤，首先需要准备以下内容。  
* **FriendlyNameForSource:**可以将此友好名称应用于配置了虚拟机的存储帐户，以便存储来自 Azure 诊断的信息
* **StorageAccountName:**在配置 Azure 诊断时指定的存储帐户的名称。  
* **StorageKey:**在此虚拟机中，存储 Azure 诊断信息的存储帐户的存储密钥。  

执行下列步骤可获取存储密钥：
 1. 浏览到 [Azure 门户](http://portal.azure.com)。
 2. 在 Azure 控制台的导航窗格中，滚动到底部并单击“更多服务”

 ![更多服务](./media/security-azure-log-integration-get-started/more-services.png)
 3. 在“筛选器”文本框中，输入“存储”。 单击“存储帐户”（输入“存储”后会出现此内容）

   ![筛选器框](./media/security-azure-log-integration-get-started/filter.png)
 4. 之后会显示存储帐户列表，请双击分配给日志存储的帐户。

   ![存储帐户列表](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. 单击“设置”部分中的“访问密钥”。

  ![访问密钥](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. 复制“密钥1”，并将其放在安全的位置，以供下一步时访问。

   ![两个访问密钥](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. 在安装了 Azure 日志集成的服务器上，打开提升权限的命令提示符（请注意此处使用的是提升权限的命令提示符窗口，而不是提升权限的 PowerShell 控制台）。
 8. 导航到 **c:\Program Files\Microsoft Azure Log Integration**
 9. 运行 ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` </br> 例如 ``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``如果希望将订阅 ID 显示在事件 XML 中，请将订阅 ID 追加到友好名称中：``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>``。或者例如 ``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]  
最多等待 60 分钟，并查看从存储帐户拉取的事件。 若要查看，请在 Azlog 集成器上打开“事件查看器 > Windows 日志 > 已转发事件”。

可在此处观看视频，回顾上面介绍的步骤。
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>如果在“已转发事件”文件夹中未显示数据，该怎么办？
如果在 1 小时之后，在“**已转发事件**”文件夹中仍未显示数据，请执行以下操作：

1. 检查运行 Azure 日志集成服务的计算机，并确保它可以访问 Azure。 若要测试连接，请尝试从浏览器打开 [Azure 门户](http://portal.azure.com)。
2. 请确保用户帐户 **Azlog** 具有对文件夹 **users\Azlog** 的写入权限。
  <ol type="a">
   <li>打开“Windows 资源管理器” </li>
  <li> 导航到“c:\users” </li>
  <li> 右键单击“c:\users\Azlog” </li>
  <li> 单击“安全”  </li>
  <li> 单击“NT 服务\Azlog”，并检查帐户的权限。 如果此选项卡中没有帐户，或者如果当前未显示相应权限，可以在此选项卡中授予帐户权限。</li>
  </ol>
3. 请确保在运行命令 **Azlog source list** 时，会列出通过命令 **Azlog source add** 添加的存储帐户。
4. 转到“**事件查看器 > Windows 日志 > 应用程序**”以查看是否存在从 Azure 日志集成报告的任何错误。


如果在安装和配置过程中遇到问题，请打开[支持请求](../azure-supportability/how-to-create-azure-support-request.md)，选择“日志集成”作为需要请求支持的服务。

另一个支持选项是 [Azure 日志集成 MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration)。 在论坛中大家可以对如何充分利用 Azure 日志集成进行提问和回答，查看提示和技巧，以此相互支持。 此外，Azure 日志集成团队也会关注此论坛，并尽可能地提供帮助。

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 日志集成的详细信息，请参阅以下文档：

* [适用于 Azure 日志的 Microsoft Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324) - 下载中心，其中包含有关 Azure 日志集成的详细信息、系统要求和安装说明。
* [Azure 日志集成简介](security-azure-log-integration-overview.md)– 本文档介绍 Azure 日志集成、其主要功能及其工作原理。
* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) - 此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。 这是关于如何配置 SIEM 组件的当前指导。 有关其他详细信息，请先与 SIEM 供应商联系。
* [Azure 日志集成常见问题解答 (FAQ)](security-azure-log-integration-faq.md) - 此常见问题解答回答了有关 Azure 日志集成的问题。
* [集成安全中心警报与 Azure 日志集成](../security-center/security-center-integrating-alerts-with-log-integration.md) - 本文档介绍如何将安全中心警报以及由 Azure 诊断和 Azure 活动日志收集的虚拟机安全事件与日志分析或 SIEM 解决方案同步。
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/)（Azure 诊断和 Azure 审核日志的新功能）– 此博客文章介绍 Azure 审核日志和其他功能，可帮助你深入了解 Azure 资源的操作。

