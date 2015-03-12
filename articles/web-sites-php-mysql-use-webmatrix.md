<properties linkid="develop-php- Website-with-mysql-and-webmatrix" urlDisplayName="Web w/ WebMatrix" pageTitle="使用 MySQL 和 WebMatrix 的 PHP 网站 - Azure 教程" metaKeywords="" description="本教程演示如何使用免费的 WebMatrix IDE 创建和部署  在 MySQL 中存储数据的 PHP 网站。" metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create and deploy a PHP-MySQL Azure  Website using WebMatrix" authors="" solutions="" manager="" editor="mollybos" />





# 使用 WebMatrix 创建和部署 PHP-MySQL Azure 网站

本教程将介绍如何使用 WebMatrix 开发 PHP-MySQL 应用程序并将其部署到 Azure 网站。WebMatrix 是 Microsoft 提供的一种免费 Web 开发工具，其中包含  网站开发所需的一切。WebMatrix 支持 PHP 并包含用于 PHP 开发的智能感知。

本教程假定您的计算机上已安装 [MySQL][install-mysql]，以便能本地测试应用程序。但是，您可以在没有安装 MySQL 的情况下完成本教程。您可以改为将应用程序直接部署到 Azure 网站。

完成本指南之后，您将拥有一个在 Azure 中运行的 PHP/MySQL 网站。
 
您将了解到以下内容：

* 如何使用管理门户创建 Azure 网站和 MySQL 数据库。由于在 Azure 网站中默认启用 PHP，因此运行 PHP 代码没有任何特殊要求。
* 如何使用 WebMatrix 开发 PHP 应用程序。
* 如何使用 WebMatrix 将应用程序发布和重新发布到 Azure。
 
通过按照本教程中的说明进行操作，您将在 PHP 中构建简单的 Tasklist Web 应用程序。将在 Azure 网站中托管应用程序。以下是正在运行的应用程序的屏幕快照：

![Azure PHP  Website][running-app]

> [AZURE.NOTE]
> 要完成本教程，您需要一个 Azure 帐户。您可以 <a href="/zh-cn/pricing/member-offers/msdn-benefits-details/">激活 MSDN 订户权益</a> 或 <a href="/zh-cn/pricing/1rmb-trial/">注册免费试用版</a>。
> 
> 如果您想要在注册帐户之前开始使用 Azure 网站，请转到 <a href="https://trywebsites.chinacloudsites.cn/?language=php">https://trywebsites.chinacloudsites.cn</a>其中您可以在 Azure 网站中立即免费创建短期 ASP.NET 入门网站。无需信用卡，没有承诺。

## 先决条件

1. [下载][tasklist-mysql-download] Tasklist 应用程序文件。Tasklist 应用程序是一个简单的 PHP 应用程序，可利用该应用程序在任务列表中添加和删除项目以及将项目标记为完成。任务列表项存储在 MySQL 数据库中。此应用程序包含以下文件：

	* **additem.php**：向列表中添加一个项。
	* **createtable.php**：创建用于此应用程序的 MySQL 表。该文件只能被调用一次。
	* **deleteitem.php**：删除项目。
	* **getitems.php**：获取数据库中的所有项目。
	* **index.php**：显示任务并提供用于向列表添加项目的窗体。
	* **markitemcomplete.php**：将项目的状态更改为已完成。
	* **taskmodel.php**：包含用于在数据库中添加、获取、更新和删除项目的函数。

1. 创建一个名为 `tasklist` 的本地 MySQL 数据库。您可以从 WebMatrix（在根据下面的教程安装它之后）中的数据库工作区或者从包含此命令的 MySQL 命令提示符执行此操作：

		mysql> create database tasklist;

	此步骤仅在你需要本地测试应用程序时是必需的。

<h2><a id="Create Website"></a>创建 Azure 网站和 MySQL 数据库</h2>

1. 登录到[管理门户][Azure-portal]。
1. 单击该门户左下的"+ 新建"图标。

	![Create New Azure  Website][New Website1]

1. 单击"网站"，然后单击"自定义创建"。

	![Custom Create a new  Website][New Website2]

	> [AZURE.NOTE]
	> 创建网站后，无法为网站创建 MySQL 数据库。您必须按照以下步骤创建网站和 MySQL 数据库。

1. 在"URL"中输入值，从"数据库"下拉列表中选择"新建 MySQL 数据库"，然后在"区域"下拉列表中选择网站的数据中心。单击对话框底部的箭头。

	![Fill in  Website details][New Website3]

5. 输入数据库的"名称"值，在"区域"下拉列表中为数据库选择数据中心，并选中表明你同意法律条款的框。单击对话框底部的复选标记。

	![Create new MySQL database][New Website4]

	创建了网站之后，您将看到文本"创建网站 '[SITENAME]' 成功"。

	接下来，您需要获取 MySQL 连接信息。


6. 单击网站列表中显示的网站的名称以打开网站的"快速启动"仪表板。

	![Open  Website dashboard][New Website5]

7. 单击"配置"选项卡。

	![Configure tab][New Website6]

8. 向下滚动到"连接字符串"部分。值 `Database`、`Data Source`、`User Id` 和 `Password`（分别）是数据库名称、服务器名称、用户名和用户密码。记下数据库连接信息，因为以后还需要此信息。

	![Connection string][ConnectionString]

## 安装 WebMatrix 和开发应用程序

您可以从[管理门户][preview-portal]安装 WebMatrix。 

1. 登录后，导航到网站的"快速启动"页面，并单击该页底部的"WebMatrix"图标：

	![Install WebMatrix][InstallWebMatrix]

	按照提示操作以安装 WebMatrix。

2. 安装 WebMatrix 之后，它会尝试将您的网站作为 WebMatrix 项目打开。你可以选择直接编辑你的实时网站或者下载一个本地副本。对于本教程中，选择 'Edit local copy'。 

3. 当提示下载网站时，请选择"是，从模板库安装"。

	![Download  Website][download-site]

4. 从可用模板中选择 **PHP**。

	![Site from template][site-from-template]

5. 选择"空网站"模板。提供网站的名称并单击"下一步"。

	![Provide name for site][site-from-template-2]

	将在 WebMatrix 上打开你的网站，其中包含一些默认文件。

	在下面的几个步骤中，您将通过添加之前下载的文件并进行一些修改来开发 Tasklist 应用程序。但是，可以添加您自己的现有文件，也可以创建新的文件。

6. 通过单击"添加现有"来添加应用程序文件：

	![WebMatrix - Add existing files][edit_addexisting]

	在生成的对话框中，导航到之前下载的文件，选择所有这些文件并单击"打开"。系统提示时，选择替换 `index.php` 文件。 

7. 接下来，您需要将本地 MySQL 数据库连接信息添加到 `taskmodel.php` 文件。双击 `taskmodel.php` 文件将其打开，并更新 `connect` 函数中的数据库连接信息。（**注意**：如果不想在本地测试您的应用程序，而是直接发布到 Azure 网站，请跳转到[发布您的应用程序](#Publish) 。）

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	保存 `taskmodel.php`文件。

8. 对于要运行的应用程序，需要创建 `items` 表。右键单击 `createtable.php` 文件并选择"在浏览器中启动"。这将在浏览器中启动 `createtable.php`，并执行在 `tasklist` 数据库中创建 `items` 表的代码。

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. 现在，你可以本地测试应用程序。右键单击 `index.php` 文件并选择"在浏览器中启动"。通过添加项目、将项目标记为已完成并删除项目来测试应用程序。  


<h2><a id="Publish"></a>发布应用程序</h2>

在将应用程序发布到 Azure 网站之前，需要将`taskmodel.php` 中的数据库连接信息更新为之前获取的连接信息（在创建 [Azure 网站  ]部分(#Create Website) 部分）。

1. 双击 `taskmodel.php` 文件将其打开，并更新 `connect` 函数中的数据库连接信息。

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	保存 `taskmodel.php`文件。

2. 在 WebMatrix 中单击"发布"，然后在"发布预览"对话框中单击"继续"。

	![WebMatrix - Publish][edit_publish]

3. 导航到 http://[your_Website_name].chinacloudsites.cn/createtable.php 以创建 the `items` 表。

4. 最后，导航到 http://[your_Website_name].chinacloudsites.cn/index.php 以使用应用程序。
	
## 修改并重新发布应用程序

您可以通过编辑之前下载的网站的本地副本轻松地对您的应用程序进行编辑并重新发布，或者可以直接在远程模式下进行编辑。在此，您将简单更改 `index.php` 文件中的标题，然后将其直接保存到实时网站。

1. 在 WebMatrix 中单击你的网站的"远程"选项卡，然后选择"打开远程视图"。这将打开您的远程网站以便直接进行编辑。
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. 通过双击 `index.php` 文件将其打开。
	![WebMatrix - Open index file][Remote_editIndex]

3. 在 **title** 和 **h1** 标记中将 **My ToDo List** 更改为 **My Task List** 并保存文件。


4. 在完成保存后，单击"运行"按钮以便查看实时网站上的更改。
	![WebMatrix - Launch site in Remote][Remote_run]


# 后续步骤

您已了解如何创建网站并将网站从 WebMatrix 部署到 Azure。若要了解有关 WebMatrix 的详细信息，请参阅下列资源：

* [WebMatrix for Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [WebMatrix  网站](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





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
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png

[Azure-portal]: https://manage.windowsazure.cn
