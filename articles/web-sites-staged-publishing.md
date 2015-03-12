<properties linkid="web-sites-staged-publishing" urlDisplayName="如何在 Microsoft Azure 上将站点分阶段" pageTitle="在 Microsoft Azure 网站上的分阶段部署" metaKeywords="Microsoft Azure Websites, Staged Deployment, Site Slots" description="了解如何在 Microsoft Azure 网站上使用分阶段发布。" metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Websites" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

<a name="Overview"></a>
# 在 Microsoft Azure 网站上的分阶段部署#
将应用程序部署 Azure 网站时，您可以部署到单独的部署槽（而不是默认生产槽中），这都是具有自己的主机名的实际站点。在**标准** web 托管计划中提供了此选项。而且，可以在两个部署槽（包括生产槽）之间交换站点和站点配置。将应用程序部署到部署槽具有以下优点：

- 您可以在分阶段部署槽中验证网站更改，然后将其与生产槽交换。

- 交换后，具有以前分阶段站点的槽现在具有以前的生产站点。如果交换到生产槽的更改与您的预期不同，您可以立即执行同一交换来收回"上一已知的良好站点"。 
 
- 首先将站点部署到槽，然后将其交换到生产，这确保槽的所有实例都预热，然后交换到生产。部署您的网站时，这消除了停机时间。流量重定向是无缝的，且不会因交换操作而删除任何请求。 

除了生产槽，**标准**计划中的每个网站都支持四个部署槽。 

## 目录 ##
- [向网站中添加部署槽](#Add)
- [关于部署槽配置](#AboutConfiguration)
- [要交换部署槽](#Swap)
- [要将生产站点回滚到过渡环境](#Rollback)
- [删除站点槽](#Delete)
- [适用于站点槽的 Azure PowerShell cmdlet](#PowerShell)
- [适用于站点槽的 Azure 跨平台命令行接口 (xplat-cli) 命令](#CLI)

<a name="Add"></a>
## 将部署槽添加到网站##

该网站必须以**标准**托管计划运行，才能启用多个部署槽。 

1. 在"快速入门"页面或网站"仪表板"页面的"速览"部分中，单击**添加新部署槽**。 
	
	![Add a new deployment slot][QGAddNewDeploymentSlot]
	
	> [AZURE.NOTE]
	> 如果网站不在**标准**模式下，您将收到消息**您必须在标准模式下才能启用过渡发布**。此时，您可以选择**升级**，并导航到网站的**缩放**选项卡，然后再继续。
	
2. 在**添加新的部署槽位**对话框中，为插槽提供一个名称，并选择是否要从另一个现有部署槽克隆网站配置。单击复选标记以继续。 
	
	![Configuration Source][ConfigurationSource1]
	
	第一次，创建一个插槽，您只有两种选择：从生产中的默认插槽克隆配置或者完全不进行克隆。 
	
	创建多个插槽后，您将能够从生产槽以外的槽克隆配置：
	
	![Configuration sources][MultipleConfigurationSources]

3. 在网站列表中，展开网站名称左侧的标记以显示部署槽。它将具有网站名称，然后是部署槽名称。 
	
	![Site List with Deployment Slot][SiteListWithStagedSite]
	
4. 单击部署站点槽的名称时，就像任何其他网站一样，将打开一个页面，其中有一组选项卡。 <strong><i>您的网站名</i>(<i>部署槽名</i>)</strong> 将出现在门户页顶部以提醒您：您正在查看部署站点槽。
	
	![Deployment Slot Title][StagingTitle]
	
5. 单击仪表板视图中的站点 URL。请注意部署槽具有其自己的主机名，也是活动站点。要限制对部署槽的公共访问权限，请参阅 [Azure 网站 - 阻止对非生产部署槽的 web 访问](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)。

	-	 

没有任何内容。您可以从其他存储库分支或完全不同的存储库部署到槽。您还可以更改此槽的配置。使用与内容更新部署槽关联的发布配置文件或部署凭证。例如，您可以使用 git [发布到此槽](/zh-cn/documentation/articles/web-sites-publish-source-control/)。

<a name="AboutConfiguration"></a>
## 关于部署槽配置##
从另一个部署槽克隆配置时，可以编辑克隆的配置。以下列表显示交换槽时将更改的配置。

**槽交换时会更改的配置**：

- 常规设置
- 连接字符串
- 处理程序映射
- 监视和诊断设置

**槽交换时不会更改的配置**：

- 发布终结点
- 自定义域名
- SSL 证书和绑定
- 缩放设置

**说明**：

- **标准** web 托管计划中的站点才提供了多个部署槽。

- 您的站点具有多个插槽时，不能更改托管计划。

- 要交换到生产环境的槽应完全按照生产环境中的预期设置进行配置。

- 默认情况下，部署槽与生产站点将指向同一个数据库。但是，您可以通过更改部署槽的数据库连接字符串，将部署槽配置为指向替代数据库。您随后可以在将部署槽交换到生产环境之前，还原部署槽的原始数据库连接字符串。


<a name="Swap"></a>
## 要交换部署槽##

1. 要交换部署槽，请在网站列表中选择您想要交换的部署槽，然后单击命令栏中的**交换**按钮。 
	
	![Swap Button][SwapButtonBar]
	
2. 将显示"交换部署"对话框。该对话框用于选择哪个站点槽应为源，哪个站点应为目标。
	
	![Swap Deployments Dialog][SwapDeploymentsDialog]
	
3. 单击复选标记完成操作。操作完成后，即已交换站点槽。


<a name="Rollback"></a>
## 要将生产站点回滚到过渡环境##
如果在槽交换后在生产中发现任何错误，请立即通过交换相同的两个槽来将槽回滚到交换前状态。 

<a name="Delete"></a>
## 要删除站点槽##

在 Azure 网站门户页面底部的命令栏中，单击**删除**。您可以选择删除网站和所有部署槽，或仅删除部署槽。 

![Delete a Site Slot][DeleteStagingSiteButton]


**说明**：

- 缩放不适用于非生产槽。它仅适用于生产槽。

- 非生产槽不支持链接的资源管理。 

- 但您仍可以根据需要直接发布到生产槽。

- 默认情况下，部署槽（站点）与生产槽（站点）共享相同的资源，并在相同虚拟机上运行。在过渡槽上运行压力测试时，生产环境将受到相当的压力负载。 

<!--	
	> [WACOM.NOTE] 在 [Azure 预览门户](https://portal.azure.com)中，您可以通过暂时将非生产槽移到其他 Web 托管计划来避免对生产槽造成这种潜在影响。请注意，测试和生产槽必须再一次共享同一个 Web 托管计划，然后才可以将测试槽交换到生产。
-->
<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>
## 适用于站点槽的 Azure PowerShell cmdlet 

Azure PowerShell 是一个模块，可提供 cmdlet 来通过 Windows PowerShell 管理 Azure，包括对管理 Azure 部署槽的支持。 Website。 

- 有关安装和配置 Azure PowerShell 的信息以及使用 Windows Azure 订阅对 Azure PowerShell 进行身份验证的信息，请参阅[如何安装和配置 Windows Azure PowerShell](http://www.windowsazure.cn/zh-cn/documentation/articles/install-configure-powershell)。  

- 要列出 PowerShell 中可用于 Azure 网站的 cmdlet，请调用 `help Azure Website`。 

----------

### Get-Azure Website
**Get Azure Website** cmdlet 提供了有关当前订阅的 Azure 的信息 Website如在以下示例中。 

`Get-Azure Website siteslotstest`

----------

### New-Azure Website
您可以使用 **New-Azure Website** cmdlet 并指定站点和槽的名称以"标准"模式为任何网站创建站点槽。还表示与用于创建部署槽的站点相同的区域，如以下示例所示。 

`New-Azure Website siteslotstest -Slot staging -Location "China East"`

----------

### Publish-Azure WebsiteProject
您可以使用 **Publish-Azure WebsiteProject** cmdlet 进行内容部署，如以下示例中所示。 

`Publish-Azure WebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

----------

### Show-Azure Website
内容和配置更新应用到新的槽之后，您可以通过使用 **Show-Azure Website** cmdlet 浏览到该槽，对更新进行验证，如以下示例中所示。

`Show-Azure Website -Name siteslotstest -Slot staging`

----------

### Switch-Azure WebsiteSlot
**Switch-Azure WebsiteSlot** cmdlet 可以执行交换操作，使更新的部署槽成为生产站点，如以下示例所示。生产站点将不会停机，也不会进行冷启动。 

`Switch-Azure WebsiteSlot -Name siteslotstest`

----------

### Remove-Azure Website
如果不再需要部署槽，则可以使用 **Remove-Azure Website** cmdlet 删除该部署槽，如以下示例所示。

`Remove-Azure Website -Name siteslotstest -Slot staging` 

----------

<!-- ======== XPLAT-CLI =========== -->

<a name="CLI"></a>
## 适用于站点槽的 Azure 跨平台命令行接口 (xplat-cli) 命令

Azure 跨平台命令行接口 (xplat-cli) 提供了用于与 Azure 结合使用的跨平台命令，包括对在 Azure 网站上管理部署槽的支持。 

- 有关安装和配置 xplat-cli 的说明（包括有关如何将 xplat-cli 连接到 Azure 订阅的信息），请参阅[安装和配置 Azure 跨平台命令行接口](/zh-cn/documentation/articles/xplat-cli/)。

-  要在 xplat-cli 中列出可用于 Azure 网站的命令，请调用 `azure site -h`。 

----------
### azure site list
有关当前订阅中 Azure 网站的相关信息，请调用 **azure site list**，如以下示例所示。
 
`azure site list siteslotstest`

----------
### azure site create
要为"标准"模式下的任何网站创建站点槽，请调用 **azure site create**，并指定现有站点的名称和要创建的槽的名称，如以下示例所示。

`azure site create siteslotstest --slot staging`

要启用新槽源代码管理，请使用 **--git** 选项，如以下示例所示。
 
`azure site create --git siteslotstest --slot staging`

----------
### azure site swap
要使更新的部署槽成为生产站点，请使用 **azure site swap** 命令执行交换操作，如以下示例所示。生产站点将不会停机，也不会进行冷启动。 

`azure site swap siteslotstest`

----------
### azure site delete
要删除不再需要的部署槽，请使用 **azure site delete** 命令，如以下示例所示。

`azure site delete siteslotstest --slot staging`

----------
## 后续步骤 ##
[Azure 网站 - 阻止对非生产部署槽的 web 访问](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

[Microsoft Azure 免费试用版](/zh-cn/pricing/1rmb-trial/)


<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
