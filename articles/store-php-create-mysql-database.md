<properties
	pageTitle="在 Azure 中创建 MySQL 数据库并连接到它"
	description="了解如何使用 Azure 门户创建 MySQL 数据库，然后通过 Azure 中的 PHP Web 应用连接到该数据库。"
	documentationCenter="php"
	services="app-service\web"
	authors="cephalin"
	manager="wpickett"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.date="06/03/2016"
	wacn.date=""/>

# 在 Azure 中创建 MySQL 数据库并连接到它

本指南将演示如何在 [Azure 门户](https://manage.windowsazure.cn)中创建 MySQL 数据库（提供程序是 [ClearDB](http://www.cleardb.com/)），以及如何从 [Azure App Service](./app-service/app-service-value-prop-what-is.md) 中运行的 PHP Web 应用连接到该数据库。

> [AZURE.NOTE] 你还可以创建 MySQL 数据库作为[应用商店应用模板](/documentation/articles/app-service-web-create-web-app-from-marketplace/)的一部分。

## 在 Azure 门户中创建 MySQL 数据库

若要在 Azure 门户中创建 MySQL 数据库，请执行以下操作：

1. 登录到 [Azure 门户](https://manage.windowsazure.cn)。

2. 从左侧菜单中，单击“新建”>“数据 + 存储”>“MySQL 数据库”。

	![在 Azure 门户中创建 MySQL 数据库 - 开始](./media/store-php-create-mysql-database/create-db-1-start.png)

2. 在“新建 MySQL 数据库”[边栏选项卡](azure-portal-overview.md)中，按如下所示配置新的 MySQL 数据库（边栏选项卡：水平打开的门户页）：

	- **数据库名称**：键入可唯一标识的名称
	- **订阅**：选择要使用的订阅
	- **数据库类型**：对于低成本或免费层选择“共享”，或者选择“专用”以获取专用资源。
	- **资源组**：将 MySQL 数据库添加到现有[资源组](../resource-group-overview.md)，或将其放入新资源组。同一组中的资源可以轻松地一起管理。
	- **位置**：选择离你近的位置。添加到现有资源组时，你会被锁定到该资源组的位置。
	- **定价层**：单击“定价层”，然后选择一个定价选项（**Mercury** 层是免费层），然后单击“选择”。
	- **法律条款**：单击“法律条款”，查看购买详细信息，然后单击“购买”。
	- **固定到仪表板**：如果你想要直接从仪表板访问它，请选择此项。如果你尚不熟悉门户导航，这将非常有用。
	
    下面的屏幕截图只是举例说明如何配置 MySQL 数据库。   
	![在 Azure 门户中创建 MySQL 数据库 - 配置](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. 完成配置后，请单击“创建”。

	![在 Azure 门户中创建 MySQL 数据库 - 创建](./media/store-php-create-mysql-database/create-db-3-create.png)

	你将看到弹出通知，告知你部署已启动。

	![在 Azure 门户中创建 MySQL 数据库 - 正在进行](./media/store-php-create-mysql-database/create-db-4-started-status.png)

	部署成功后，你将收到另一个弹出通知。该门户还将自动打开 MySQL 数据库边栏选项卡。

## 从 Azure 中的 PHP Web 应用连接到你的 MySQL 数据库

若要查看新的 MySQL 数据库的连接信息，只需单击“属性”。如果看不到“设置”边栏选项卡，请单击“设置”>“属性”。
	
![在 Azure 中创建 MySQL 数据库 - MySQL 数据库边栏选项卡](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

现在可以在任何 Web 应用中使用该连接信息。[此处](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)提供了一个示例，演示如何通过简单的 PHP 应用使用连接信息。

### 连接 Laravel Web 应用（摘自 PHP 入门教程）

假设你刚完成了教程[创建、配置 PHP Web 应用，并将其部署到 Azure](./app-service-web/app-service-web-php-get-started.md) 并且有正在 Azure 中运行的 [Laravel](https://www.laravel.com/) Web 应用，你可以轻松地向 Laravel 应用添加数据库功能。只需按以下步骤进行配置：

>[AZURE.NOTE] 以下步骤假设你已完成教程[创建、配置 PHP Web 应用，并将其部署到 Azure](./app-service-web/app-service-web-php-get-started.md)。

1. 在本地开发环境中将 Laravel 应用配置为指向 MySQL 数据库。为此，请从 Laravel 应用的根目录打开 `.env`，并配置 MySQL 数据库选项。

		DB_CONNECTION=mysql
		DB_HOST=<HOSTNAME_from_properties_blade>
		DB_PORT=<PORT_from_properties_blade>
		DB_DATABASE=<see_note_below>
		DB_USERNAME=<USERNAME_from_properties_blade>
		DB_PASSWORD=<PASSWORD_from_properties_blade>

	>[AZURE.NOTE] 在“属性”边栏选项卡中，MySQL 数据库的名称不一定是“数据库名称”字段中显示的名称。最好检查一下“连接字符串”字段中的 Database 参数。
	>
	>![在 Azure 门户中创建 MySQL 数据库 - 正在进行](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. 现在，验证你是否具有 MySQL 访问权限的最快捷方法是使用 [Laravel 的默认身份验证基架](https://laravel.com/docs/5.2/authentication#authentication-quickstart)。在命令行终端中，从 Laravel 应用的根目录运行以下命令：

		 php artisan migrate
		 php artisan make:auth

	第一个命令基于 `database/migrations` 目录中预定义的迁移在 Azure 中创建表，第二个命令搭建基本视图和路由的基架以用于用户注册和身份验证。

3. 现在运行开发服务器：

		php artisan serve

4. 在浏览器中，导航到 http://localhost:8000 并注册一个新用户，如下所示：

	![连接到 Azure 中的 MySQL 数据库 - 注册用户](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

	按照 UI 提示完成注册。注册完成后，你将登录。
	
	![连接到 Azure 中的 MySQL 数据库 - 注册用户](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

	现在将针对 Azure 中的 MySQL 数据库开发应用。

5. 现在，只需将 `.env` 设置复制到 Azure Web 应用。运行以下 Azure CLI 命令：

		azure site appsetting add DB_CONNECTION=mysql
		azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
		azure site appsetting add DB_PORT=<PORT_from_properties_blade>
		azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
		azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
		azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

	在[配置 Azure Web 应用](./app-service-web/app-service-web-php-get-started.md#configure)中了解其工作方式。

6. 接下来，将先前在运行 `php artisan make:auth` 时所做的本地更改提交并推送到 Azure。

		git add .
		git commit -m "scaffold auth views and routes"
		git push azure master

7. 浏览到 Azure Web 应用。

		azure site browse

8. 使用先前创建的用户凭据登录。

	![连接到 Azure 中的 MySQL 数据库 - 浏览到 Azure Web 应用](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

	在登录后，你应看到友好的登录后屏幕。
	
	![连接到 Azure 中的 MySQL 数据库 - 已登录](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

	祝贺你，Azure 中你的 PHP Web 应用现在正在访问你的 MySQL 数据库中的数据。

## 后续步骤

有关详细信息，请参阅 [PHP 开发中心](/develop/php/)。

<!---HONumber=AcomDC_0718_2016-->