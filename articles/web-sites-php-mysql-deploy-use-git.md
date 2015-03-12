<properties linkid="develop-php- Website-with-mysql-and-git" urlDisplayName="Web w/ MySQL + Git" pageTitle="使用 MySQL 和 Git 的 PHP 网站 - Azure 教程" metaKeywords="" description="本教程演示如何创建在 MySQL 中存储数据的 PHP 网站并使用 Git 部署到 Azure。" metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure  Website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

# 创建 PHP-MySQL Azure 网站并使用 Git 进行部署

本教程演示如何创建 PHP-MySQL Azure 网站以及如何使用 Git 部署该网站。您将使用计算机上已安装的 [PHP][install-php]、MySQL 命令行工具（[MySQL][install-mysql] 的一部分）、Web 服务器和 [Git][install-git]。本教程中的说明适用于任何操作系统，包括 Windows、Mac 和 Linux。完成本指南，您将拥有在 Azure 中  运行的 PHP/MySQL 网站。
 
您将了解到以下内容：

* 如何使用 Azure 管理门户创建 Azure 网站和 MySQL 数据库。由于在 Azure 网站中默认启用 PHP，因此运行 PHP 代码没有任何特殊要求。
* 如何使用 Git 将应用程序发布和重新发布到 Azure。
 
通过按照本教程中的说明进行操作，您将在 PHP 中构建简单的注册 Web 应用程序。将在 Azure 网站中托管应用程序。以下是已完成应用程序的屏幕快照：

![Azure PHP  Website][running-app]

> [AZURE.NOTE]
> 若要完成本教程，你需要一个启用了 Azure 网站功能的 Azure 帐户。如果没有帐户，则可创建一个免费的试用帐户，只需几分钟即可完成。有关详细信息，请参阅： <a href="http://www.windowsazure.cn/zh-cn/pricing/1rmb-trial/?WT.mc_id=A74E0F923" target="_blank">Azure 免费试用版</a>。
> 
> 如果您想要在注册帐户之前开始使用 Azure 网站，请转到 <a href="https://trywebsites.chinacloudsites.cn/?language=php">https://trywebsites.chinacloudsites.cn</a>其中您可以在 Azure 网站中立即免费创建短期 ASP.NET 入门网站。无需信用卡，没有承诺。

## 设置开发环境

本教程假定您已在计算机上安装 [PHP][install-php]、MySQL 命令行工具（[MySQL][install-mysql] 的一部分）、Web 服务器和 [Git][install-git]。

> [AZURE.NOTE]
> 如果是在 Windows 上执行本教程，则可通过安装 Azure SDK for PHP 为 PHP 设置计算机并自动配置 IIS（Windows 中的 <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">内置 Web 服务器</a>。

## <a id="create-web-site-and-set-up-git"></a>创建 Azure 网站并设置 Git 发布

按照以下步骤创建 Azure 网站和 MySQL 数据库：

1. 登录到 [Azure 管理门户][management-portal]
2. 单击该门户左下的"+ 新建"图标。

	![Create New Azure  Website][new-Website]

3. 单击"网站"，然后单击"自定义创建"。

	![Custom Create a new  Website][custom-create]
	
	在"URL"中输入值，从"数据库"下拉列表中选择"新建 MySQL 数据库"，然后在"区域"下拉列表中选择网站的数据中心。单击对话框底部的箭头。

	![Fill in  Website details][Website-details]

4. 为数据库的"名称"输入一个值，在"区域"下拉列表中为数据库选择数据中心，并选中表明你同意法律条款的框。单击对话框底部的复选标记。

	![Create new MySQL database][new-mysql-db]

	创建网站后，您将看到文本"创建网站 "[SITENAME]" 成功完成"。现在，您可以启用 Git 发布。

6. 单击网站列表中显示的网站的名称以打开该网站的"快速启动"仪表板。

	![Open  Website dashboard][go-to-dashboard]


7. 在"快速启动"页的底部，单击"设置 Git 发布"。 

	![Set up Git publishing][setup-git-publishing]

8. 若要启用 Git 发布，必须提供用户名和密码。记下你创建的用户名和密码。（如果之前已设置 Git 存储库，则将跳过此步骤。）

	![Create publishing credentials][credentials]

	设置存储库需要花费几秒钟的时间。

9. 在您的存储库已就绪后，将显示有关将应用程序文件推送到存储库的说明。记下这些说明 - 稍后您将使用它们。

	![Git instructions][git-instructions]

## 获取远程 MySQL 连接信息

若要连接到正在 Azure 网站中运行的 MySQL 数据库，你将需要连接信息。若要获取 MySQL 连接信息，请按照以下步骤操作：

1. 从网站的仪表板中，单击页面右侧的"查看连接字符串"链接：

	![Get database connection information][connection-string-info]
	
2. 记下 `Database`、`Data Source`、`User Id` 和 `Password` 的值。

## 本地构建和测试应用程序

现在你已创建 Azure 网站，可以本地开发应用程序，然后在测试后部署该应用程序。 

注册应用程序是一个简单的 PHP 应用程序，它使您能够通过提供您的姓名和电子邮件地址来注册事件。有关以前的注册者的信息将显示在表中。注册信息将存储在 MySQL 数据库中。应用程序由一个文件组成（复制/粘贴以下可用代码）：

* **index.php**：将显示注册形式及包含注册者信息的表。

若要本地构建和运行应用程序，请执行下列步骤。请注意，这些步骤假定你已在本地计算机上设置 PHP、MySQL 命令行工具（MySQL 的一部分）和 Web 服务器，并且你已启用 [MySQL 的 PDO 扩展][pdo-mysql]。

1. 使用之前检索的 `Data Source`、`User Id`、`Password` 和 `Database` 的值，连接到远程 MySQL 服务器：

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. 此时将出现 MySQL 命令提示符：

		mysql>

3. 粘贴以下 `CREATE TABLE` 命令以在您的数据库中创建 `registration_tbl` 表：

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. 在 Web 服务器的根目录中，创建一个名为 `registration` 的文件夹，并在该文件夹中创建一个名为 `index.php` 的文件。

5. 在文本编辑器或 IDE 中打开 **index.php** 文件，添加以下代码，并完成用 `//TODO:` 注释标记的必需更改。


		<html>
		<head>
		<Title>Registration Form</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php
			// DB connection info
			//TODO: Update the values for $host, $user, $pwd, and $db
			//using the values you retrieved earlier from the portal.
			$host = "value of Data Source";
			$user = "value of User Id";
			$pwd = "value of Password";
			$db = "value of Database";
			// Connect to database.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insert registration info
			if(!empty($_POST)) {
			try {
				$name = $_POST['name'];
				$email = $_POST['email'];
				$date = date("Y-m-d");
				// Insert data
				$sql_insert = "INSERT INTO registration_tbl (name, email, date) 
						   VALUES (?,?,?)";
				$stmt = $conn->prepare($sql_insert);
				$stmt->bindValue(1, $name);
				$stmt->bindValue(2, $email);
				$stmt->bindValue(3, $date);
				$stmt->execute();
			}
			catch(Exception $e) {
				die(var_dump($e));
			}
			echo "<h3>Your're registered!</h3>";
			}
			// Retrieve data
			$sql_select = "SELECT * FROM registration_tbl";
			$stmt = $conn->query($sql_select);
			$registrants = $stmt->fetchAll(); 
			if(count($registrants) > 0) {
				echo "<h2>People who are registered:</h2>";
				echo "<table>";
				echo "<tr><th>Name</th>";
				echo "<th>Email</th>";
				echo "<th>Date</th></tr>";
				foreach($registrants as $registrant) {
					echo "<tr><td>".$registrant['name']."</td>";
					echo "<td>".$registrant['email']."</td>";
					echo "<td>".$registrant['date']."</td></tr>";
		    	}
		 		echo "</table>";
			} else {
				echo "<h3>No one is currently registered.</h3>";
			}
		?>
		</body>
		</html>

你现在可以浏览到 **http://localhost/registration/index.php** 来测试此应用程序。


## 发布应用程序

在本地测试你的应用程序之后，你可以使用 Git 将其发布到 Azure 网站。你将初始化本地 Git 存储库并发布该应用程序。

> [AZURE.NOTE]
> 这些步骤与在门户中的"创建 Azure 网站并设置 Git 发布"一节的结尾显示的步骤相同。

1. （可选）如果你忘记或误放了 Git 远程存储库 URL，请导航到门户上的"部署"选项卡。
	
	![Get Git URL][git-instructions]

1. 打开 GitBash（或终端，如果 Git 在您的 `PATH` 中），将目录更改为应用程序的根目录，并运行以下命令：

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	系统将提示你输入之前创建的密码。

	![Initial Push to Azure via Git][git-initial-push]

2. 浏览至 **http://[sitename].chinacloudsites.cn/index.php** 以开始使用应用程序（此信息将存储在您的帐户仪表板上）：

	![Azure PHP  Website][running-app]

发布应用程序之后，你可以开始对其进行更改并使用 Git 发布所做的更改。 

## 发布对应用程序所做的更改

若要发布对应用程序所做的更改，请执行下列步骤：

1. 本地对应用程序进行更改。
2. 打开 GitBash（或终端，如果 Git 在您的 `PATH` 中），将目录更改为应用程序的根目录，并运行以下命令：

		git add .
		git commit -m "comment describing changes"
		git push azure master

	系统将提示你输入之前创建的密码。

	![Pushing site changes to Azure via Git][git-change-push]

3. 浏览到 **http://[sitename].chinacloudsites.cn/index.php** 以查看您的应用程序及可能做出的任何更改：

	![Azure PHP  Website][running-app]

4. 您也可以在 Azure 管理门户的  'Deployments' 选项卡上查看新的部署：

	![List of  Website deployments][deployments-list]

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/zh-cn/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/zh-cn/download/details.aspx?id=20098
[install-git]: http://git-scm.com/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-Website]: ./media/web-sites-php-mysql-deploy-use-git/new_Website.jpg
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
[Website-details]: ./media/web-sites-php-mysql-deploy-use-git/Website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://manage.windowsazure.cn
[sql-database-editions]: http://msdn.microsoft.com/library/azure/ee621788.aspx
