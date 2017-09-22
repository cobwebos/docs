---
title: "创建和管理混合连接 | Microsoft Docs"
description: "了解如何创建混合连接、管理连接以及安装混合连接管理器。 MABS，WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: b1f22104d530315318685fa4896b710b003ac3cc
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---
# <a name="create-and-manage-hybrid-connections"></a>创建和管理混合连接

> [!IMPORTANT]
> BizTalk 混合连接已停用，并由应用服务混合连接替代。 有关详细信息，包括如何管理现有 BizTalk 混合连接，请参阅 [Azure App Service 混合连接](../app-service/app-service-hybrid-connections.md)。


## <a name="overview-of-the-steps"></a>步骤概述
1. 通过在专用网络中为本地资源输入**主机名**或 **FQDN**，来创建混合连接。
2. 将 Azure Web 应用或 Azure 移动应用链接到混合连接。
3. 将混合连接管理器安装在本地资源上，并连接到特定混合连接。 Azure 门户提供单击体验以便进行安装和连接。
4. 管理混合连接及其连接密钥。

本主题列出了这些步骤。 

> [!IMPORTANT]
> 可以将混合连接终结点设置为 IP 地址。 如果使用的是 IP 地址，则并非一定会连接本地资源，具体取决于客户端。 混合连接取决于执行 DNS 查找的客户端。 在大多数情况下，**客户端**是应用程序代码。 如果客户端不执行 DNS 查找（它不会尝试解析 IP 地址，就像它是域名 (x.x.x.x) 一样），则不会通过混合连接发送流量。
> 
> 例如（伪代码），将 **10.4.5.6** 定义为本地主机：
> 
> **以下方案可以运行：**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **以下方案无法运行：**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>创建混合连接
可以在 Azure 门户中使用 Web 应用**或**使用 BizTalk 服务创建混合连接。 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

**若要在 BizTalk 服务中创建混合连接**：

1. 登录到 [Azure 经典门户](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左侧导航窗格中，选择“BizTalk 服务”，并选择 BizTalk 服务。 
   
    如果没有现有的 BizTalk 服务，则可以[创建 BizTalk 服务](biztalk-provision-services.md)。
3. 选择“混合连接”选项卡：  
   ![“混合连接”选项卡][HybridConnectionTab]
4. 选择“创建混合连接”，或选择任务栏中的“添加”按钮。 输入以下内容：
   
   | 属性 | 说明 |
   | --- | --- |
   | Name |混合连接名称必须是唯一的，并且名称不能与 BizTalk 服务名称相同。 可以输入任何名称，但是尽量使其目的明确。 示例包括：<br/><br/>Payroll*SQLServer*<br/>SupplyList*SharepointServer*<br/>Customers*OracleServer* |
   | 主机名 |输入完全限定的主机名或本地资源的 IPv4 地址。 示例包括：<br/><br/>mySQLServer<br/>*mySQLServer*.*Domain*.corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*yourCompany*.com<br/>10.100.10.10<br/><br/>如果使用的是 IPv4 地址，请注意客户端或应用程序代码可能不会解析 IP 地址。 请参阅本主题顶部的重要事项。 |
   | 端口 |输入本地资源的端口号。 例如，如果使用的是 Web 应用，则输入端口 80 或端口 443。 如果使用的是 SQL Server，则输入端口 1433。 |
5. 选择复选标记以完成设置。 

#### <a name="additional"></a>其他
* 可创建多个混合连接。 请参阅 [BizTalk 服务：版本图表](biztalk-editions-feature-chart.md)，了解允许的连接数。 
* 每个混合连接均使用连接字符串对进行创建：用于发送的应用程序密钥和用于侦听的本地密钥。 每一对都有主要密钥和辅助密钥。 

## <a name="LinkWebSite"></a>链接 Azure App Service Web 应用或移动应用
要将 Azure 应用服务中的 Web 应用或移动应用链接到现有混合连接，请在“混合连接”边栏选项卡中选择“使用现有混合连接”。 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>本地安装混合连接管理器
创建混合连接后，将混合连接管理器安装在本地资源上。 它可以从 Azure Web 应用或从 BizTalk 服务下载。 BizTalk 服务步骤： 

1. 登录到 [Azure 经典门户](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左侧导航窗格中，选择“BizTalk 服务”，并选择 BizTalk 服务。 
3. 选择“混合连接”选项卡：  
   ![“混合连接”选项卡][HybridConnectionTab]
4. 在任务栏中，选择“本地安装”：  
   ![本地安装][HCOnPremSetup]
5. 选择“安装和配置”以在本地系统上运行或下载混合连接管理器。 
6. 选择复选标记以启动安装。 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>其他
* 混合连接管理器可以安装在以下操作系统上：
  
  * Windows Server 2008 R2（.NET Framework 4.5+ 和 Windows Management Framework 4.0+ 必需）
  * Windows Server 2012（Windows Management Framework 4.0+ 必需）
  * Windows Server 2012 R2
* 安装混合连接管理器后，将发生以下情况： 
  
  * 在 Azure 上托管的混合连接会自动配置为使用主应用程序连接字符串。 
  * 本地资源会自动配置为使用主本地连接字符串。
* 混合连接管理器必须使用有效的本地连接字符串才能进行授权。 Azure Web 应用或移动应用必须使用有效的应用程序连接字符串才能进行授权。
* 通过在另一台服务器上安装混合连接管理器的另一实例，可以扩展混合连接。 配置本地侦听器以便将相同的地址用作第一个本地侦听器。 在此情况下，流量随机分布（轮循机制）在活动的本地侦听器之间。 

## <a name="ManageHybridConnection"></a>管理混合连接
要管理混合连接，可以：

* 使用 Azure 门户并转到 BizTalk 服务。 
* 使用 [REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)。

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>复制/重新生成混合连接字符串
1. 登录到 [Azure 经典门户](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左侧导航窗格中，选择“BizTalk 服务”，并选择 BizTalk 服务。 
3. 选择“混合连接”选项卡：  
   ![“混合连接”选项卡][HybridConnectionTab]
4. 选择混合连接。 在任务栏中，选择“管理连接”：  
   ![管理选项][HCManageConnection]
   
    “管理选项”列出应用程序和本地连接字符串。 可以复制连接字符串或重新生成在连接字符串中使用的访问密钥。 
   
    **如果选择“重新生成**”，将更改在连接字符串内使用的共享访问密钥。 请执行以下操作：
   
   * 在 Azure 经典门户中，选择 Azure 应用程序中的“同步密钥”。
   * 重新运行“本地安装”。 重新运行本地安装时，本地资源会自动配置为使用更新的主连接字符串。

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>使用组策略以控制混合连接所使用的本地资源
1. 下载[混合连接管理器管理模板](http://www.microsoft.com/download/details.aspx?id=42963)。
2. 解压缩文件。
3. 在修改组策略的计算机上，执行以下操作：  
   
   * 将 .ADMX 文件复制到 *%WINROOT%\PolicyDefinitions* 文件夹。
   * 将 .ADML 文件复制到 *%WINROOT%\PolicyDefinitions\en-us* 文件夹。

复制后，可以使用组策略编辑器更改策略。

## <a name="next"></a>下一步
[混合连接概述](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>另请参阅
[用于在 Microsoft Azure 上管理 BizTalk 服务的 REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk 服务：版本图表](biztalk-editions-feature-chart.md)  
[使用 Azure 经典门户创建 BizTalk 服务](biztalk-provision-services.md)  
[Biztalk 服务：“仪表板”、“监视”和“缩放”选项卡](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 

