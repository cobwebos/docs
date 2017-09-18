---
title: "在 Azure 门户中创建 Azure BizTalk 服务 | Microsoft Docs"
description: "了解如何在 Azure 门户中预配或创建 Azure BizTalk 服务；MABS、WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 3ad18876-a649-40d6-9aa0-1509c1d62c43
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: mandia
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: eca77b4a82eb67e1755717bb4429f8d450a64dc5
ms.contentlocale: zh-cn
ms.lasthandoff: 09/18/2017

---
# <a name="create-biztalk-services-using-the-azure-portal"></a>使用 Azure 门户创建 BizTalk 服务

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]


> [!TIP]
> 若要登录 Azure 门户，需要使用 Azure 帐户和 Azure 订阅。 如果没有帐户，则可以创建一个免费的试用帐户，只需几分钟即可完成。 请参阅 [Azure 免费试用](http://go.microsoft.com/fwlink/p/?LinkID=239738)。


## <a name="CreateService"></a>创建 BizTalk 服务
不一定能够使用所有的 BizTalk 服务设置，这取决于选择的版本。

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在导航窗格的底部，选择“新建”：  
   ![选择“新建”按钮][NEWButton]
3. 选择“应用服务” > “BizTalk 服务” > “自定义创建”：  
   ![选择“BizTalk 服务”，并选择“自定义创建”][NewBizTalkService]
4. 输入 BizTalk 服务设置：
   
    <table border="1">
    <tr>
    <td><strong>BizTalk 服务名称</strong></td>
    <td>可以输入任何名称，但请尽量具体。 示例包括：<br/><br/>
    <em>mycompany</em>.biztalk.windows.net<br/>
    <em>mycompanymyapplication</em>.biztalk.windows.net<br/>
    <em>myapplication</em>.biztalk.windows.net<br/><br/>“.biztalk.windows.net”会自动添加到输入的名称上。 此时会创建一个用于访问 BizTalk 服务的 URL，例如 <strong>https://<em>myapplication</em>.biztalk.windows.net</strong>。
    </td>
    </tr>
    <tr>
    <td><strong>版本</strong></td>
    <td>如果处在开发/测试阶段，则选择“开发人员版”。<strong></strong> 如果处在生产阶段，则使用 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk 服务：版本图表</a>来确定是“高级版”、“标准版”还是“基本版”适合业务方案。<strong></strong><strong></strong><strong></strong>
    </td>
    </tr>
    <tr>
    <td><strong>区域</strong></td>
    <td>选择托管 BizTalk 服务的地理区域。</td>
    </tr>
    <tr>
    <td><strong>域 URL</strong></td>
    <td><strong>可选</strong>。 默认情况下，域 URL 为 <em>YourBizTalkServiceName</em>.biztalk.windows.net。 也可以输入自定义域。 例如，如果域是 <em>contoso</em>，则可输入： <br/><br/>
    <em>MyCompany</em>.contoso.com<br/>
    <em>MyCompanyMyApplication</em>.contoso.com<br/>
    <em>MyApplication</em>.contoso.com<br/>
    <em>YourBizTalkServiceName</em>.contoso.com<br/>
    </td>
    </tr>
    </table>
选择“下一步”箭头。
5. 输入存储和数据库设置：  <table border="1">
    <tr>
    <td><strong>监视/存档存储帐户</strong></td>
    <td>选择一个现有存储帐户，或创建一个新的存储帐户。 <br/><br/>如果要创建新的存储帐户，请输入“存储帐户名称”。<strong></strong></td>
    </tr>
    <tr>
    <td><strong>跟踪数据库</strong></td>
    <td>如果要使用现有的 Azure SQL 数据库，需保证它没有被其他 BizTalk 服务使用。 需要使用创建该 Azure SQL 数据库服务器时输入的登录名和密码。<br/><br/><strong>提示</strong>：在与 BizTalk 服务相同的区域中创建跟踪数据库和监视/存档存储帐户。</td>
    </tr>
    </table>
选择“下一步”箭头。
6. 输入数据库设置：  <table border="1">
    <tr>
    <td><strong>Name</strong></td>
    <td>如果在上一屏幕中选择了“创建新的 SQL 数据库实例”，会提供此设置。<strong></strong>
    <br/><br/>
输入 BizTalk 服务要使用的 SQL 数据库名称。</td>
    </tr>
    <tr>
    <td><strong>服务器</strong></td>
    <td>如果在上一屏幕中选择了“创建新的 SQL 数据库实例”，会提供此设置。<strong></strong>
    <br/><br/>
选择现有的 SQL 数据库服务器或创建新的 SQL 数据库服务器。</td>
    </tr>
    <tr>
    <td><strong>服务器登录名</strong></td>
    <td>输入登录用户名。</td>
    </tr>
    <tr>
    <td><strong>服务器登录密码</strong></td>
    <td>输入登录密码。</td>
    </tr>
    <tr>
    <td><strong>区域</strong></td>
    <td>如果选择了“创建新的 SQL 数据库实例”，会提供此设置。<strong></strong> 选择用于托管 SQL 数据库的地理区域。</td>
    </tr>
    </table>

单击复选标记以完成向导操作。 将显示进度图标：  
![完成时显示进度图标][ProgressComplete]

完成向导操作后，即会创建该 Azure BizTalk 服务，它随时可用于应用程序。 默认设置足以满足需要。 如果想要更改默认设置，请在左侧导航窗格中选择“BizTalk 服务”，然后选择 BizTalk 服务。 其他设置在顶部的 [“仪表板”、“监视”和“缩放”选项卡](biztalk-dashboard-monitor-scale-tabs.md) 中显示。

根据 BizTalk 服务的状态，某些操作可能无法完成。 有关这些操作的列表，请转到 [BizTalk 服务状态图表](biztalk-service-state-chart.md)。

## <a name="post-provisioning-steps"></a>预配后的步骤
* [在本地计算机上安装证书](#InstallCert)
* [添加生产就绪证书](#AddCert)
* [获取 Access Control 命名空间](#ACS)

#### <a name="InstallCert"></a>在本地计算机上安装证书
自签名证书是 BizTalk 服务预配的一部分，会随 BizTalk 服务订阅创建和关联。 必须下载此证书，然后将它安装到在其中部署 BizTalk 服务应用程序或将消息发送到 BizTalk 服务终结点的计算机上。

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左侧导航窗格中选择“BizTalk 服务”，然后选择 BizTalk 订阅。
3. 选择“仪表板”选项卡  。
4. 选择“下载 SSL 证书”：  
   ![修改 SSL 证书][QuickGlance]
5. 双击该证书，并运行向导以安装证书。 确保在“受信任的根证书颁发机构”  存储下安装该证书。

#### <a name="AddCert"></a>添加生产就绪证书
在创建 BizTalk 服务时自动创建的自签名证书仅限在开发环境中使用。 对于生产方案，使用生产就绪证书替换它。

1. 在“仪表板”选项卡上，选择“更新 SSL 证书”。
2. 浏览到包括 BizTalk 服务名称的专有 SSL 证书 (*CertificateName*.pfx)，输入密码，并单击复选标记。

#### <a name="ACS"></a>获取访问控制命名空间
1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=213885)。
2. 在左侧导航窗格中选择“BizTalk 服务”，然后选择 BizTalk 服务。
3. 在任务栏中，选择“连接信息”：  
   ![选择“连接信息”][ACSConnectInfo]
4. 复制 Access Control 值。

从 Visual Studio 中部署 BizTalk 服务项目时，进入此 Access Control 命名空间。 系统会自动为 BizTalk 服务创建访问控制命名空间。

Access Control 值可用于任何应用程序。 创建 Azure BizTalk 服务时，此 Access Control 命名空间将控制向 BizTalk 服务部署进行的身份验证。 如果要更改订阅或管理命名空间，则在左侧导航窗格中选择“Active Directory”，然后选择命名空间。 任务栏列出了选项。

单击“管理”  会打开访问控制管理门户。 在访问控制管理门户中，BizTalk 服务使用“服务标识”：  
![访问控制管理门户中的 ACS 服务标识][ACSServiceIdentities]

Access Control 服务标识是一组凭据，这些凭据允许应用程序或客户端使用 Access Control 直接进行身份验证并接收令牌。

> [!IMPORTANT]
> BizTalk 服务将“所有者”用于默认服务标识以及“密码”值。 如果使用“对称密钥”值而不是“密码”值，则可能会发生以下错误。<br/><br/>*无法使用指定的凭据连接到 Access Control 管理服务帐户*
> 
> 

[管理 ACS 命名空间](https://msdn.microsoft.com/library/azure/hh674478.aspx) 列出了一些指导和建议。

## <a name="requirements-explained"></a>要求说明
下列要求不适用于免费版。

<table border="1">
<tr bgcolor="FAF9F9">
        <td><strong>所需条件</strong></td>
        <td><strong>为何需要</strong></td>
</tr>
<tr>
<td>Azure 订阅</td>
<td>订阅可以确定谁可以登录 Azure 门户。 帐户持有人可在 <a HREF="https://account.windowsazure.com/Subscriptions"> Azure 订阅</a>中创建订阅。
<br/><br/>
Azure 帐户可以有多个订阅，只要使用者获得许可，就可以管理这些帐户。 例如，Azure 帐户持有人创建一个名为 <em>BizTalkServiceSubscription</em> 的订阅，并向贵公司内的 BizTalk 管理员（例如，ContosoBTSAdmins@live.com）授予对此订阅的访问权限。 在这种情况下，BizTalk 管理员可登录 Azure 门户，并对订阅中的所有托管服务（包括 Azure BizTalk 服务）拥有完全管理员权限。 BizTalk 管理员不是 Azure 帐户持有人，因此无法访问任何结算信息。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=267577">在 Azure 门户中管理订阅和存储帐户</a>提供了详细信息。
</td>
</tr>
<tr>
<td>Azure SQL 数据库</td>
<td>存储由 BizTalk 服务（包括跟踪数据）使用的表、视图以及存储过程。
<br/><br/>
在创建 BizTalk 服务时，可以使用现有的 Azure SQL Server 和 Azure SQL 数据库，也可以自动创建新的 Azure SQL Server 或 Azure SQL 数据库。
<br/><br/>
自动配置 SQL 数据库规模。 通常，默认的规模就足以满足 BizTalk 服务的需要。 更改规模会影响定价。 请参阅 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=234930">Accounts and Billing in Azure SQL Database</a>（Azure SQL 数据库中的帐户和计费）
<br/><br/>
<strong>说明</strong>
<br/>
<ul>
<li> 创建新的 Azure SQL Server 和 Database 时，会自动启用 Azure 服务。 BizTalk 服务要求启用 Azure 服务。</li>
<li>如果在现有 Azure SQL Server 的基础上创建新的 Azure SQL 数据库，不会更改服务器的防火墙规则。 因此，可能不允许其他 Azure 服务访问服务器的数据库。</li>
</ul>
</td>
</tr>
<tr>
<td>Azure Access Control 命名空间</td>
<td>向 Azure BizTalk 服务进行身份验证。 从 Visual Studio 中部署 BizTalk 服务项目时，进入此 Access Control 命名空间。 创建 BizTalk 服务时，会自动创建 Access Control 命名空间。</td>
</tr>

<tr>
<td>Azure 存储帐户</td>
<td>提供对 BizTalk 服务使用的表、Blob 和队列的访问权限，以执行以下操作：

<ul>
<li>保存用于监视 BizTalk 服务的日志文件。 监视输出也会在 Azure 门户的“监视”选项卡中显示。</li>
<li>在合作伙伴之间创建 X12 或 AS2 协议时，可以启用“存档”功能来存储消息属性。 此数据保存在该存储帐户中。</li>
</ul>
<br/>
创建 BizTalk 服务时，可以使用现有的存储帐户，也可以自动创建新的存储帐户。
<br/><br/>
默认的存储设置就足以满足 BizTalk 服务的需要。
<br/><br/>
创建存储帐户时，会自动创建主密钥和辅助密钥。 这些密钥控制对存储帐户的访问。 BizTalk 服务自动使用主密钥。
<br/><br/>
有关详细信息，请参阅<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285671">存储</a>。
</td>
</tr>

<tr>
<td>SSL 专用证书</td>
<td>
创建 Azure BizTalk 服务时，会同时创建包含 BizTalk 服务名称的 HTTPS URL。 此 URL 已自动配置为使用仅供开发的自签名证书。 对于生产目的，需要使用私有 SSL 证书。
<br/><br/>
<strong>重要的 SSL 证书信息</strong>

<ul>
<li>证书到期日期必须低于 5 年。</li>
<li>所有私有证书都需要密码。 应该知道此密码，并且最好与管理员共享此密码。</li>
<li>自签名证书需在测试/开发环境中使用。 使用自签名证书时，请将证书导入到“个人”证书存储和“受信任的根证书颁发机构”证书存储中。</li>
</ul>
<br/>将生产证书请求发送到证书颁发机构时，请提供以下证书属性：
<br/>

<ul>
<li><strong></strong>增强型密钥使用：Azure BizTalk 服务要求至少进行服务器身份验证。</li>
<li><strong></strong>公用名：请输入 Azure BizTalk 服务 URL 的完全限定域名 (FQDN)。 请参阅本文中的<a HREF="#CreateService">创建 BizTalk 服务</a>。</li>
</ul>
<br/>
创建 BizTalk 服务后，便可以添加新的或不同的证书。
</td>
</tr>
</table>
<!---Loc Comment: Please, check link [Create a BizTalk Service] since it is not redirecting to any location.--->



## <a name="hybrid-connections"></a>混合连接
创建 Azure BizTalk 服务时，会出现“混合连接”选项卡：

![混合连接选项卡][HybridConnectionTab]

使用混合连接可将 Azure 网站或 Azure 移动服务连接到使用静态 TCP 端口的任何本地资源，例如 SQL Server、MySQL、HTTP Web API、移动服务和大多数自定义 Web 服务。  混合连接不同于 BizTalk 适配器服务。 BizTalk 适配器服务用于将 Azure BizTalk 服务连接到本地业务线 (LOB) 系统。

 有关详细信息，包括如何创建和管理混合连接，请参阅 [混合连接](integration-hybrid-connection-overview.md) 。

## <a name="next-steps"></a>后续步骤
现在已创建 BizTalk 服务，可以让自己熟悉以下各个选项卡： [Biztalk 服务：“仪表板”、“监视”和“缩放”选项卡](biztalk-dashboard-monitor-scale-tabs.md)。 BizTalk 服务已准备就绪，可用于应用程序了。 若要开始创建应用程序，请转到 [Azure BizTalk 服务](http://go.microsoft.com/fwlink/p/?LinkID=235197)。

## <a name="see-also"></a>另请参阅
* [BizTalk 服务：版本图表](biztalk-editions-feature-chart.md)<br/>
* [BizTalk 服务状态图表](biztalk-service-state-chart.md)<br/>
* [BizTalk 服务：备份和还原](biztalk-backup-restore.md)<br/>
* [BizTalk 服务：限制](biztalk-throttling-thresholds.md)<br/>
* [BizTalk 服务：颁发者名称和颁发者密钥](biztalk-issuer-name-issuer-key.md)<br/>
* [如何开始使用 Azure BizTalk 服务 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [混合连接](integration-hybrid-connection-overview.md)

[NewBizTalkService]: ./media/biztalk-provision-services/WABS_NewBizTalkService.png
[NEWButton]: ./media/biztalk-provision-services/WABS_New.png
[ProgressComplete]: ./media/biztalk-provision-services/WABS_ProgressComplete.png
[ACSConnectInfo]: ./media/biztalk-provision-services/WABS_ACSConnectInformation.png
[QuickGlance]: ./media/biztalk-provision-services/WABS_QuickGlance.png
[ACSServiceIdentities]: ./media/biztalk-provision-services/WABS_ACSServiceIdentities.png
[HybridConnectionTab]: ./media/biztalk-provision-services/WABS_HybridConnectionTab.png

