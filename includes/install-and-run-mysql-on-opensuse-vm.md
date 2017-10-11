
1. 若要提升特权，请键入：
   
        sudo -s
   
    输入你的密码。
2. 若要安装 MySQL Community Server 版本，请键入：
   
        zypper install mysql-community-server
   
    在下载和安装 MySQL 时所等待。
3. 若要设置 MySQL 系统引导时启动，请键入：
   
        insserv mysql
4. 使用以下命令手动启动 MySQL daemon (mysqld):
   
        rcmysql start
   
    若要检查 MySQL 守护程序的状态，请键入：
   
        rcmysql status
   
    若要停止 MySQL 守护程序，请键入：
   
        rcmysql stop
   
   > [!IMPORTANT]
   > 安装完成后，MySQL 根密码默认为空。 我们建议你运行**mysql\_安全\_安装**，可帮助保护 MySQL 的脚本。 该脚本将提示您更改 MySQL 根密码、 删除匿名用户帐户、 禁用远程根登录、 删除测试数据库和重新加载权限表。 我们建议你对所有这些选项回答是，并更改根密码。
   > 
   > 
5. 键入以下内容来运行脚本 MySQL 安装脚本：
   
        mysql_secure_installation
6. 登录到 MySQL:
   
        mysql -u root -p
   
    输入 MySQL 根密码 （这在上一步中更改了），然后你将会看到一条提示其中你可以发出 SQL 语句以便与数据库交互。
7. 若要创建新的 MySQL 用户，运行以下命令在**mysql >**提示符：
   
        CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    请注意，用分号 （;） 在行末尾至关重要对于结束命令。
8. 若要创建数据库，并授予`mysqluser`用户访问它的权限，发出下列命令：
   
        CREATE DATABASE testdatabase;
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
   
    请注意，数据库用户名和密码仅由使用连接到数据库的脚本。  数据库名称不一定表示系统上的实际用户帐户的用户帐户。
9. 若要从另一台计算机登录，请键入：
   
        GRANT ALL ON testdatabase.* TO 'mysqluser'@'<ip-address>' IDENTIFIED BY 'password';
   
    其中`ip-address`是将连接到 MySQL 的计算机的 IP 地址。
10. 若要退出 MySQL 数据库管理实用程序，请键入：
    
        quit

## <a name="add-an-endpoint"></a>添加一个终结点
1. 安装 MySQL 后，你将需要配置一个终结点，以便远程访问 MySQL。 登录到[Azure 经典门户][AzurePortal]。 单击**虚拟机**，单击你的新虚拟机的名称，然后单击**终结点**。
2. 单击**添加**位于页的底部。
3. 添加终结点协议的名为"MySQL" **TCP**，和**公共**和**私有**端口设置为"3306"。
4. 若要从你的计算机远程连接到虚拟机，键入：
   
        mysql -u mysqluser -p -h <yourservicename>.cloudapp.net
   
    例如，使用我们在本教程中创建的虚拟机时，键入以下命令：
   
        mysql -u mysqluser -p -h testlinuxvm.cloudapp.net

[MySQLDocs]: http://dev.mysql.com/doc/
[AzurePortal]: http://manage.windowsazure.com

[Image9]: ./media/install-and-run-mysql-on-opensuse-vm/LinuxVmAddEndpointMySQL.png
