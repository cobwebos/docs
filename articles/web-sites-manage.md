<properties linkid="manage-scenarios-how-to-manage-websites" urlDisplayName="如何管理" pageTitle="如何管理  网站 - Microsoft Azure 服务管理" metaKeywords="Azure portal  Website management" description="Microsoft Azure 中的"门户网站"管理页的引用。为每个网站管理页提供了详细信息。" metaCanonical="" services="web-sites" documentationCenter="" title="How to Manage  Websites" authors="timamm"  solutions="" writer="timamm" manager="" editor=""  />



# <a name="howtomanage"></a>通过"Azure 管理门户"管理网站

您可以使用一组页面或"选项卡"在 Azure 门户中管理您的网站。下面将介绍各网站管理页。

## 快速启动 ##
**快速启动**管理页包括以下部分：

- **获取工具** - 提供[安装 WebMatrix][mswebmatrix]和[Microsoft Azure SDK][azuresdk]的链接。
- **发布应用** - 提供下载网站的发布配置文件、为网站重置部署凭据、在非过渡网站上添加过渡发布（部署）槽以及了解过渡发布信息的链接。
- **集成源代码管理** - 从源代码管理工具或网站（例如 TFS、CodePlex、GitHub、Dropbox、Bitbucket 或本地 Git）设置和管理部署。

## 仪表板 ##
**仪表板**管理页包括以下内容：

一个将网站使用率作为特定度量值进行汇总的图表。

 - **CPU 时间** - 对网站的 CPU 使用率的度量。
 - **输入的数据** - 对网站从客户端收到的数据的度量。
 - **输出的数据** - 对网站发送到客户端的数据的度量。
 - **HTTP 服务器错误数** - 已发送的 HTTP"5xx 服务器错误"消息数。
 - **请求** - 客户端向网站发出的所有请求数。

**注意：** 您可以通过在**监视**管理页选择页面底部的**添加度量值**来添加其他性能度量值。更多详细信息请参阅[如何监视网站][监视器]。

**Web 终结点状态** - 已经为监视配置的 Web 终结点的列表。如果尚未配置终结点，则单击**配置 Web 终结点监视**，然后转到**配置**管理页的**监视**部分。更多详细信息，请参阅[如何监视网站][监视器]。

**自动缩放状态** - 在"标准"模式下，您可以自动缩放您的资源，这些您可以按需支出。若要启用自动缩放，请选择**配置自动缩放**，这将显示**缩放**页。如果您的网站处于"免费"或"共享"模式，您需要首先将其更改为"标准"模式（可以在**缩放**页上执行此操作），然后才能配置自动缩放。**"自动缩放操作日志**可打开**管理服务**门户，您可以从中查看网站的自动缩放历史记录。默认查询针对最近 24 小时，但您可以修改查询。

**使用概览** - 此部分显示网站的 CPU、文件系统和内存使用情况的统计信息。

**链接的资源** - 此部分显示连接到您的网站的资源（如 SQL 或 MySQL 数据库、Microsoft Azure 存储帐户）列表。单击资源的名称以便管理该资源。如果您具有一个 MySQL 数据库，则单击其名称将显示 ClearDB 管理页。您可以从该管理页看到您的性能度量值，或者转到 ClearDB 仪表板，从中可以根据需要升级您的 MySQL 数据库。如果未列出任何资源，请单击**管理链接的资源**，转到**链接的资源**页，然后添加网站资源链接。

**速览**部分包含以下摘要信息和链接（根据您的设置，下面列出的某些选项可能不会显示）：

 - **查看适用的外接程序** - 打开**从应用商店购买**对话框，选择购买可为您的网站提供附加功能的外接程序。在您的区域或环境中，某些外接程序可能不可用。
 - **查看连接字符串** - 查看网站的数据库连接字符串。
 - **下载发布配置文件** - 单击此链接以下载网站发布配置文件。发布配置文件包含您的凭据（用户名和密码）和 Url，  用于通过 FTP 和 Git 将内容上载到您的网站。配置文件为 XML 格式，可以在文本编辑器中进行查看。
 - **设置部署凭据** - 单击以创建通过 FTP 或 Git 向网站中上载内容时使用的用户名和密码。可以使用这些凭据将内容推送到订阅的任何网站。（请参阅[FTP 凭据]。）**注意**：不支持通过使用 Microsoft 帐户 (Live ID) 凭据对 FTP 主机或 Git 存储库进行身份验证。
 - **重置发布配置文件凭据** - 重置网站的发布配置文件。之前下载的发布配置文件将无效。
 - **从源代码管理设置部署** - 显示一个对话框，从中可以从 Team Foundation Service、CodePlex、GitHub、Dropbox、Bitbucket 或本地 Git 设置连续发布。
 - **添加新的部署槽** - 对于标准模式下的站点，可以使用此功能来创建站点过渡槽。过渡槽（过渡站点）可用于在将站点交换到生产环境之前验证站点内容和配置。您还可以使用站点的过渡版本逐渐添加内容更新，在过渡槽上完成更新之后将站点交换到生产环境。（您不能向位于过渡槽中的站点添加槽。）
 - **在 Visual Studio Online 中在线编辑** - 单击此链接可使用 Visual Studio Online 从 Microsoft Azure 门户直接在线编辑您的网站。此选项将不会显示，直到您在**配置**页面启用该选项。

<!--
 - **从 Dropbox 断开连接** - 如果您已出于部署目的设置了与 Dropbox 的连接，此链接将允许您断开与其的连接。
 -->
 
 - **删除 Git 存储库** - 如果您已设置了 Git 存储库，该链接可用于将其删除。
 - **状态**-指示网站是否正在运行。
 - **管理服务** - 单击**操作日志**链接以查看您从 Microsoft Azure 管理服务门户执行的网站操作日志。
 - **虚拟 IP 地址** - 如果您已在**配置**选项卡的**SSL 绑定**部分为网站配置基于 IP 的 SSL 绑定，则该选项将显示网站的虚拟 IP 地址。 
 - **站点 URL** - 指定网站在 Internet 上可公开访问的地址。
 - **计算模式** - 指定网站是在"免费"、"共享"、"基本"还是"标准"模式下运行。有关 Web 缩放组模式的详细信息，请参阅[如何缩放网站][缩放]。
 - **FTP 主机名** - 指定通过 FTP 发布到网站时要使用的 URL（请参阅[FTP 凭据])。
 - **FTPS 主机名**-指定通过 FTPS 发布到网站时要使用的 URL （请参阅[FTP 凭据])。 
 - **部署用户/FTP 用户**-显示通过 FTP 或 Git 将网站部署到 Microsoft Azure 时所使用的帐户（请参阅[FTP 凭据])。
 - **FTP 诊断日志** - 指定网站诊断日志的 FTP 位置（如果已在**配置**管理页上启用了诊断日志记录）。
 - **FTPS 诊断日志** - 指定网站诊断日志的 FTPS 位置（如果已在**配置**管理页上启用了诊断日志记录）。
 - **位置**-指定托管网站的数据中心的区域。
 - **订阅名称** - 指定与网站关联的订阅名称。
 - **订阅 ID** - 指定与网站关联的订阅的唯一订阅 ID (GUID)。


## 部署##
 只有在您已经从源代码管理设置了部署后，此选项卡才出现。**部署**管理页提供了有关使用您选择的发布方法对网站进行的所有部署的摘要。如果已为网站配置了 Git 发布，但未对网站进行任何部署，则**部署**管理页将提供描述如何使用 GIT 将您的 Web 应用程序部署到网站的信息。

## 监视##
**监视**管理页提供了一个显示网站使用情况信息的图表。默认情况下，此图表显示的度量值与**仪表板**页上的图表显示的度量值相同，如上面的"仪表板"一节中所述。还可以将该图表配置为显示度量值"HTTP 成功"、"HTTP 重定向"、"HTTP 401 错误"、"HTTP 403 错误"、"HTTP 404 错误"和"HTTP 406 错误"。有关这些度量值的详细信息，请参阅[如何监视网站][监视器]。

## WebJobs##
WebJobs 管理页用于为您的网站创建按需、按计划或连续运行的任务。有关详细信息，请参阅[如何使用 Microsoft Azure 网站使用 WebJobs 功能](/ zh-cn/documentation/articles/web-sites-create-web-jobs/)。

## 配置##
**配置**管理页用于设置应用程序特定的设置，包括：

详情请参阅[如何配置网站][Configure]。


## 缩放##
在**缩放**管理页上，您可以指定 web 缩放组模式（**免费**、**共享**、**基本**，或**标准**)。**"共享**、**基本**和**标准**模式可以提供更好的吞吐量和性能。**共享**、**基本**和**标准**模式允许您增加**实例数**，即您的网站和其他网站在同一个 web 缩放组中的使用的虚拟机数。
 
在**标准**模式下，您还可以通过更改**实例大小**来增加每个实例的内核数和内存大小。为了实现更高的成本效益，您可以选择**自动缩放**选项，使 Microsoft Azure 为您的网站动态分配资源。 

有关配置网站缩放选项的详细信息，请参阅[如何缩放网站][Scale]。

## 链接的资源##
**链接的资源**管理页提供了网站正在使用的 Microsoft Azure 资源列表，包括 SQL 数据库、MySQL 数据库和 Azure 存储帐户。可单击资源名称以便管理该资源。

## 备份##
**备份**管理页可用于自动或手动创建网站备份，将网站还原到以前的状态，或基于其中一个备份创建新网站。有关详细信息，请参阅[Microsoft Azure 网站备份](http://www.windowsazure.cn/zh-cn/documentation/articles/web-sites-backup/)和[还原 Microsoft Azure 网站](http://www.windowsazure.cn/zh-cn/documentation/articles/web-sites-restore/)。

## "管理页"图标##
图标显示在网站各管理页的底部。其中一些图标显示在多页上，几个图标只显示在特定页上。**仪表板**管理页的底部显示下列图标：

- **浏览**-打开网站的默认页面。
- **关闭**-关闭网站。
- **重新启动**-重新启动网站。
- **管理域**-将域映射到该网站。**免费**缩放模式下的站点不可用。
- **删除**-删除该网站。
- **WebMatrix** -在 WebMatrix 中打开受支持的网站，允许您对网站进行更改，然后在 Microsoft Azure 上将所做的更改发布回网站。

下列图标不会显示在**仪表板**管理页的底部，而是显示在其他管理页的底部以完成特定任务：

- **添加度量值** - 该图标位于**监视**管理页的底部，它允许您将度量值添加到"监视"管理页上显示的图表中。
- **链接** - 该图标位于**链接的资源**管理页的底部，用于创建指向其他 Microsoft
-  Azure 资源的管理链接。例如，网站访问 SQL 数据库时，可以通过单击**链接**创建指向该数据库资源的管理链接。


## FTP 凭据

有两组 FTP 凭据供您使用： *deployment*凭据和 *publishing profile*凭据。他们的主要区别是：

**部署凭据**

- 与 Microsoft 帐户关联。 
- 可用于部署到与帐户关联的所有订阅中的任何网站。 
- 选择用户名/密码
- 通常用于 git 或 FTP 部署

	 
**发布配置文件凭据**

- 与单个网站相关联。 
- 您不用选择用户名或密码。
- 通常用于 Web 部署，但也可以用于 FTP。


您可以使用任意一组凭据。仪表板的**速览**部分列出了 FTP 和 FTPS 主机名。


### 使用部署凭据

设置部署凭据： 

1.	在管理门户中，转到您的网站的**仪表板**页面。
2.	单击**设置部署凭据**。
3.	在对话框中输入用户名和密码。

注意：在步骤 2 中，如果您有部署凭据，门户将提示**重置部署凭据**。单击此按钮设置一个新密码或更改用户名。

部署凭据都与 Microsoft 帐户关联。如果您更改用户名或密码，更改将适用于与帐户关联的所有网站。如果 Azure 订阅有多个管理员，每个管理员均有自己的凭据。 
完整的 FTP 用户名称是"website\username"。名称以**部署/FTP 用户**的形式列在门户的**速览**部分。


### 使用发布配置文件凭据

每个网站都有自己的发布配置文件凭据。查看凭据：

1.	在管理门户中，转到您的网站的**仪表板**页面。
2.	单击**下载发布配置文件**。

发布配置文件是一种 XML 文件。它包含两个配置文件，一个用于 Web 部署，另一个用于 FTP。

<pre>
&lt;publishData&gt;
  &lt;publishProfile
    profileName="contoso - Web Deploy"
    publishMethod="MSDeploy"
    publishUrl="contoso.scm.chinacloudsites.cn:443"
    msdeploySite="contoso"
    userName="$contoso"
    userPWD="abc1234..."
    destinationAppUrl="http://contoso.chinacloudsites.cn"
    SQLServerDBConnectionString=""
    mySQLDBConnectionString=""
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
  &lt;publishProfile 
    profileName="contoso - FTP" 
    <mark>publishMethod="FTP"</mark> 
    publishUrl="ftp://waws-prod-bay-003.ftp.azurewebsites.chinacloudapi.cn/site/wwwroot" 
    ftpPassiveMode="True" 
    <mark>userName="contoso\$contoso"</mark> 
    <mark>userPWD=" abc1234..."</mark>  
    destinationAppUrl="http://contoso.chinacloudsites.cn" 
    SQLServerDBConnectionString="" 
    mySQLDBConnectionString="" 
    hostingProviderForumLink="" 
    controlPanelLink="http://windows.azure.com"&gt;
    &lt;databases/&gt;
  &lt;/publishProfile&gt;
&lt;/publishData&gt;
</pre>

查找 <code>publishMethod ="FTP" 的配置文件</code>。  用户名显示在 <code>userName 下方</code>，密码显示在 <code>userPWD 下方</code>。

若要重置密码，请单击**重置您的发布配置文件凭据**。若要获取新的凭据，请再次下载发布配置文件。发布配置文件凭据与网站相关联。每个网站都有自己的发布配置文件。



<!-- LINKS -->
[mswebmatrix]:http://go.microsoft.com/fwlink/?LinkID=226244

[azuresdk]:/zh-cn/downloads/

[Configure]: /zh-cn/documentation/articles/web-sites-configure/

[监视器]: /zh-cn/documentation/articles/web-sites-monitor/

[Scale]: /zh-cn/documentation/articles/web-sites-scale/


<!-- Anchors. -->
[FTP 凭据]: #ftp-credentials

