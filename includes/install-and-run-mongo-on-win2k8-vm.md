请按照下列步骤来安装和运行 Windows Server 的虚拟机上运行 MongoDB。

> [!IMPORTANT]
> 默认情况下不启用 MongoDB 安全功能，例如身份验证和 IP 地址绑定。 将 MongoDB 部署到生产环境之前，应启用安全功能。  有关详细信息，请参阅[安全性和身份验证](http://www.mongodb.org/display/DOCS/Security+and+Authentication)。
>
>

1. 你已连接到使用远程桌面的虚拟机后，打开 Internet Explorer 的**启动**虚拟机上的菜单。
2. 选择**工具**右上角的按钮。  在**Internet 选项**，选择**安全**选项卡上，然后选择**受信任的站点**图标，最后单击**站点**按钮。 添加*https://\*。 mongodb.org*到受信任站点列表。
3. 转到[下载-MongoDB](https://www.mongodb.com/download-center#community)。
4. 查找**当前稳定版本**的**社区服务器**，选择最新**64 位**Windows 列中的版本。 下载，然后运行 MSI 安装程序。
5. MongoDB 通常安装在 C:\Program files\mongodb 下。 在桌面上搜索环境变量并将 MongoDB 二进制文件路径添加到路径变量。 例如，你可能在您的计算机上，在 C:\Program Files\MongoDB\Server\3.4\bin 中找到二进制文件。
6. 在数据磁盘中创建 MongoDB 数据和日志目录 (例如驱动器**f:**) 在前面的步骤中创建。 从**启动**，选择**命令提示符**打开命令提示符窗口。  键入：

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. 若要运行的数据库，运行：

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    所有日志消息都定向到*F:\MongoLogs\mongolog.log*文件当 mongod.exe 服务器启动和预分配日志文件。 它可能需要几分钟 mongodb 预分配日志文件并开始侦听连接。 MongoDB 实例正在运行时，命令提示符下保持专注于此任务。
8. 若要启动 MongoDB 管理外壳程序，打开另一个命令窗口，从**启动**并键入以下命令：

        C:\> cd \my_mongo_dir\bin  
        C:\my_mongo_dir\bin> mongo  
        >db  
        test
        > db.foo.insert( { a : 1 } )  
        > db.foo.find()  
        { _id : ..., a : 1 }  
        > show dbs  
        ...  
        > show collections  
        ...  
        > help  

    插入创建该数据库。
9. 或者，你可以将 mongod.exe 作为服务安装：

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    安装的服务名 MongoDB 为"Mongo DB"的说明。 `--logpath`选项必须用于指定日志文件，因为正在运行的服务没有在命令窗口中显示输出。  `--logappend`选项指定重新启动服务会导致输出将追加到现有的日志文件。  `--dbpath`选项指定数据目录的位置。 有关更多与服务相关的命令行选项，请参阅[与服务相关命令行选项][MongoWindowsSvcOptions]。

    若要启动服务，运行此命令：

        C:\> net start MongoDB
10. 现在，MongoDB 已安装且运行，需要在 Windows 防火墙中打开一个端口，因此你可以远程连接到 MongoDB。  从**启动**菜单上，选择**管理工具**然后**高级安全 Windows 防火墙**。
11. a） 在左窗格中，选择**入站规则**。  在**操作**窗格中，在右侧，选择**新建规则...**.

    ![Windows 防火墙][Image1]

    b） 在**新建入站规则向导**，选择**端口**，然后单击**下一步**。

    ![Windows 防火墙][Image2]

    c） 选择**TCP**然后**特定本地端口**。  指定的端口"27017"（MongoDB 侦听的默认端口），然后单击**下一步**。

    ![Windows 防火墙][Image3]

    d） 选择**允许连接**单击**下一步**。

    ![Windows 防火墙][Image4]

    e） 单击**下一步**试。

    ![Windows 防火墙][Image5]

    f） 指定规则名称，如"MongoPort"，然后单击**完成**。

    ![Windows 防火墙][Image6]

12. 如果你在创建虚拟机时未配置 MongoDB 终结点，你可以现在执行此操作。 你需要防火墙规则和终结点能够远程连接到 MongoDB。

  在 Azure 门户中，单击**虚拟机 （经典）**，单击你的新虚拟机的名称，然后单击**终结点**。

    ![终结点][Image7]

13. 单击 **“添加”**。

14. 添加名为"Mongo"，协议的终结点**TCP**，并且两个**公共**和**私有**端口设置为"27017"。 打开此端口允许 MongoDB 进行远程访问。

    ![终结点][Image9]

> [!NOTE]
> 端口 27017 是 MongoDB 使用的默认端口。 你可以通过指定更改此默认端口`--port`启动 mongod.exe 服务器时的参数。 请确保提供防火墙中的同一端口号和中前面的说明操作的"Mongo"终结点。
>
>

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/menusendpointadd.png
<!-- Removed 03/08/2017. Not in new portal. -->
<!-- [Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
-->
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/newendpointdetails.png
