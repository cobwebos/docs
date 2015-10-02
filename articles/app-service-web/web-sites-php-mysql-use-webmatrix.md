<properties 
	pageTitle="使用 WebMatrix 在 Azure App Service 中创建和部署 PHP-MySQL Web 应用" 
	description="演示如何使用免费的 WebMatrix IDE，在 Azure App Service 中创建并部署一个可在 MySQL 中存储数据的 PHP Web 应用。"
	tags="azure-portal" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.date="07/07/2015" 
	wacn.date=""/>





#使用 WebMatrix 创建和部署 PHP-MySQL Azure 网站

本教程将介绍如何使用 WebMatrix 开发 PHP-MySQL 应用程序并将其部署到 Azure 网站。WebMatrix 是 Microsoft 提供的一类免费的 Web 开发工具，此工具包含进行网站开发所需的一切。WebMatrix 支持 PHP 并包含用于 PHP 开发的智能感知。

本教程假定你的计算机上已安装 [MySQL][install-mysql]，以便能本地测试应用程序。但是，您可以在没有安装 MySQL 的情况下完成本教程。您可以改为将应用程序直接部署到 Azure 网站。

完成本指南之后，您将拥有一个在 Azure 中运行的 PHP/MySQL 网站。
 
你将学习以下内容：

* 如何使用管理门户创建 Azure 网站和 MySQL 数据库。由于在 Azure 网站中默认启用 PHP，因此运行 PHP 代码没有任何特殊要求。
* 如何使用 WebMatrix 开发 PHP 应用程序。
* 如何使用 WebMatrix 将应用程序发布和重新发布到 Azure。
 
通过按照本教程中的说明进行操作，您将在 PHP 中构建简单的 Tasklist Web 应用程序。将在 Azure 网站中托管应用程序。以下是正在运行的应用程序的屏幕快照：

![Azure PHP 网站][running-app]

> [AZURE.NOTE]
> 若要完成本教程，你需要一个 Azure 帐户。你可以<a href="/zh-cn/pricing/member-offers/msdn-benefits-details/">激活 MSDN 订户权益</a>或<a href="/zh-cn/pricing/1rmb-trial/">注册以获取免费试用版</a>。
> 
> 如果你想要在注册帐户之前开始使用 Azure 网站，请转到 <a href="https://trywebsites.chinacloudsites.cn/?language=php">https://trywebsites.chinacloudsites.cn</a>，你可以通过该网站在 Azure 网站中免费创建一个生存期较短的 ASP.NET 入门站点。你不需要使用信用卡，也不需要做出承诺。

##先决条件

1. [下载][tasklist-mysql-download] Tasklist 应用程序文件。Tasklist 应用程序是一个简单的 PHP 应用程序，可利用该应用程序在任务列表中添加和删除项目以及将项目标记为完成。任务列表项存储在 MySQL 数据库中。此应用程序包含以下文件：

	* **additem.php**：向列表中添加一个项。
	* **createtable.php**：创建用于应用程序的 MySQL 表。该文件只能被调用一次。
	* **deleteitem.php**：删除一个项。
	* **getitems.php**：获取数据库中的所有项。
	* **index.php**：显示任务并提供用于向列表添加项目的窗体。
	* **markitemcomplete.php**：将项目的状态更改为已完成。
	* **taskmodel.php**：包含用于在数据库中添加、获取、更新和删除项目的函数。

1. 创建一个名为 `tasklist` 的本地 MySQL 数据库。您可以从 WebMatrix（在根据下面的教程安装它之后）中的数据库工作区或者从包含此命令的 MySQL 命令提示符执行此操作：

		mysql> create database tasklist;

	此步骤仅在你需要本地测试应用程序时是必需的。

<h2><a id="Create Website"></a>创建 Azure 网站和 MySQL 数据库</h2>

1. 登录到[管理门户][Azure-portal]。
1. 单击该门户左下的“+ 新建”图标。
	
	![创建新的 Azure 网站][New Website1]
	
1. 单击“网站”，然后单击“自定义创建”。
	
	![自定义创建新的网站][New Website2]
	
	> [AZURE.NOTE]
	> 创建网站后，无法为网站创建 MySQL 数据库。您必须按照以下步骤创建网站和 MySQL 数据库。

1. 在“URL”中输入值，从“数据库”下拉列表中选择“新建 MySQL 数据库”，然后在“区域”下拉列表中选择网站的数据中心。单击对话框底部的箭头。

	![填写网站详细信息][New Website3]

5. 为数据库的“名称”输入一个值，在“区域”下拉列表中为数据库选择数据中心，并选中表明你同意法律条款的框。单击对话框底部的复选标记。

	![新建 MySQL 数据库][New Website4]

	创建网站后，你会看到文本“创建网站 '[SITENAME]' 已成功”。

	接下来，您需要获取 MySQL 连接信息。


6. 单击网站列表中显示的网站的名称以打开网站的“快速启动”仪表板。

	![打开网站仪表板][New Website5]

7. 单击“配置”选项卡。

	![配置选项卡][New Website6]

8. 向下滚动到“连接字符串”部分。`Database`、`Data Source`、`User Id` 和 `Password` 的值（分别）是数据库名称、服务器名称、用户名和用户密码。记下数据库连接信息，因为以后还需要此信息。

	![连接字符串][ConnectionString]

##安装 WebMatrix 和开发应用程序

你可以从 [管理门户][预览门户] 安装 WebMatrix。

1. 登录后，导航到网站的“快速启动”页面，并单击该页底部的“WebMatrix”图标：

	![安装 WebMatrix][InstallWebMatrix]

	按照提示操作以安装 WebMatrix。

2. 如果这是你首次使用 WebMatrix 3，系统将会提示你登录 Azure。您也可以单击“登录”按钮，并选择“添加帐户”。选择使用你的 Microsoft 帐户“登录”。

	![添加帐户](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. 如果你已注册某一 Azure 帐户，则可以使用你的 Microsoft 帐户登录：

	![登录 Azure](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. 在开始屏幕上，单击“新建”按钮，然后选择“模板库”，从模板库创建新站点：

	![从模板库创建新网站](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)
	
3. 当提示下载网站时，请选择“是，从模板库安装”。

	![下载网站][download-site]

4. 从可用模板中选择 **PHP**。

	![从模板创建站点][site-from-template]

5. 选择“空网站”模板。提供网站的名称并单击“下一步”。

	![提供站点的名称][site-from-template-2]

	将在 WebMatrix 上打开你的网站，其中包含一些默认文件。

	在下面的几个步骤中，您将通过添加之前下载的文件并进行一些修改来开发 Tasklist 应用程序。但是，可以添加您自己的现有文件，也可以创建新的文件。

6. 通过单击“添加现有”来添加应用程序文件：

	![WebMatrix - 添加现有文件][edit\_addexisting]

	在生成的对话框中，导航到之前下载的文件，选择所有这些文件并单击“打开”。系统提示时，选择替换 `index.php` 文件。

7. 接下来，你需要将本地 MySQL 数据库连接信息添加到 `taskmodel.php` 文件。双击 `taskmodel.php` 文件将其打开，并更新 `connect` 函数中的数据库连接信息。（**注意**：跳转到[发布应用程序](#Publish)（如果你不需要本地测试应用程序，而是希望直接发布到 Azure 网站。）

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	保存 `taskmodel.php` 文件。

8. 对于要运行的应用程序，需要创建 `items` 表。右键单击 `createtable.php` 文件并选择“在浏览器中启动”。这将在浏览器中启动 `createtable.php`，并执行在 `tasklist` 数据库中创建 `items` 表的代码。

	![WebMatrix - 在浏览器中启动 createtable.php][edit\_run]

9. 现在，你可以本地测试应用程序。右键单击 `index.php` 文件并选择“在浏览器中启动”。通过添加项目、将项目标记为已完成并删除项目来测试应用程序。


## 发布应用程序

在将应用程序发布到 Azure 网站之前，需要将 `taskmodel.php` 中的数据库连接信息更新为之前获取的连接信息（在 [创建 Azure 网站和 MySQL 数据库](#创建网站)部分）。

1. 双击 `taskmodel.php` 文件将其打开，并更新 `connect` 函数中的数据库连接信息。

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	保存 `taskmodel.php` 文件。

2. 在 WebMatrix 中单击“发布”。

	![WebMatrix - 发布][edit\_publish]

3. 单击“从 Microsoft Azure 中选择现有网站”。

3. 选择前面创建的 App Service Web 应用。

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. 不断单击“继续”，直到 WebMatrix 将站点发布到 Azure App Service Web Apps。

3. 导航到 http://[your 网站名称].azurewebsites.net/createtable.php 以创建 `items` 表。

4. 最后，导航到 http://[your 网站名称].chinacloudsites.cn/index.php 以使用应用程序。
	
##修改并重新发布应用程序

您可以通过编辑之前下载的网站的本地副本轻松地对您的应用程序进行编辑并重新发布，或者可以直接在远程模式下进行编辑。在此，你将简单更改 `index.php` 文件中的标题，然后将其直接保存到实时网站。

1. 在 WebMatrix 中单击你的网站的“远程”选项卡，然后选择“打开远程视图”。这将会打开可直接编辑的远程站点。
	![WebMatrix - 打开远程视图][OpenRemoteView]
 
2. 通过双击打开 `index.php` 文件。
	![WebMatrix - 打开索引文件][Remote\_editIndex]

3. 在 **title** 和 **h1** 标记中将 **My ToDo List** 更改为 **My Task List** 并保存文件。


4. 在完成保存后，单击“运行”按钮以便查看实时站点上的更改。
	![WebMatrix - 远程启动站点][Remote\_run]


# 后续步骤

* [WebMatrix 网站](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[New Website1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebsite1.jpg
[New Website2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebsite2.png
[New Website3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebsite3.png
[New Website4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebsite4.png
[New Website5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebsite5.png
[New Website6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebsite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit\_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit\_addexisting.png
[edit\_run]: ./media/web-sites-php-mysql-use-webmatrix/edit\_run.png
[edit\_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit\_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote\_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote\_editIndex.png
[Remote\_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote\_run.png













[Azure-portal]: https://manage.windowsazure.cn

<!---HONumber=71-->