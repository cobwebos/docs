---
title: "将 SQL Server 虚拟机设置为 IPython Notebook 服务器 | Microsoft Docs"
description: "使用 SQL Server 和 IPython Server 设置数据科学虚拟机。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1fd6014a-d180-4558-b4eb-d9b5a331a99f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: xibingao;bradsev
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 353f09a8a6fa3798d764d41618a8f0b3ba83e3d0


---
# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>将 Azure SQL Server 虚拟机设置为用于高级分析的 IPython Notebook 服务器
本主题介绍如何预配和配置 SQL Server 虚拟机，以便用作基于云的数据科学环境的一部分。 使用支持工具配置 Windows 虚拟机，如 IPython Notebook、Azure 存储资源管理器和 AzCopy 以及其他可用于对数据科学项目的实用程序。 例如，Azure 存储资源管理器和 AzCopy 提供了从本地计算机将数据上载到 Azure blob 存储或从 blob 存储下载到本地计算机的便捷方法。

Azure 虚拟机库包括多个内含 Microsoft SQL Server 的映像。 选择适合数据需要的 SQL Server VM 映像。 建议的映像如下：

* 适用于中小型数据大小的 SQL Server 2012 SP2 Enterprise
* 针对大型数据大小的数据仓库工作负荷优化的 SQL Server 2012 SP2 Enterprise
  
  > [!NOTE]
  > SQL Server 2012 SP2 Enterprise 映像**不包括数据磁盘**。 将需要添加和/或附加一个或多个虚拟硬盘来存储数据。 当你创建 Azure 虚拟机时，它具有一个映射到 C 驱动器的操作系统磁盘和一个映射到 D 驱动器的临时磁盘。 不要使用 D 驱动器来存储数据。 顾名思义，它仅提供临时存储。 它不提供冗余或备份，因为它不驻留在 Azure 存储空间中。
  > 
  > 

## <a name="a-nameprovisionaconnect-to-the-azure-classic-portal-and-provision-an-sql-server-virtual-machine"></a><a name="Provision"></a>连接到 Azure 经典门户并预配 SQL Server 虚拟机
1. 使用帐户登录到 [Azure 经典门户](http://manage.windowsazure.com/)。
   如果你没有 Azure 帐户，请访问 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
2. 在 Azure 经典门户中网页的左下角，依次单击“+新建”、“计算”、“虚拟机”和“从库中”。
3. 在“创建虚拟机”页上，基于数据需求选择包含 SQL Server 的虚拟机映像，然后单击该页右下角的“下一步”箭头。 有关在 Azure 上支持的 SQL Server 映像的最新信息，请参阅 [Azure 虚拟机中 SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294719) 文档集的 [Azure 虚拟机中 SQL Server 入门](http://go.microsoft.com/fwlink/p/?LinkId=294720)主题。
   
   ![选择 SQL Server VM][1]
4. 在第一个“虚拟机配置”页上，提供以下信息：
   
   * 提供一个“虚拟机名称”。
   * 在“新用户名”框中，键入用于 VM 本地管理员帐户的唯一用户名。
   * 在“新密码”框中，键入一个强密码。 有关详细信息，请参阅[强密码](http://msdn.microsoft.com/library/ms161962.aspx)。
   * 在“确认密码”框中，重新键入该密码。
   * 从下拉列表中选择适当的**大小**。
     
     > [!NOTE]
     > 在预配期间指定虚拟机的大小：A2 是建议用于生产工作负荷的最小大小。 在使用 SQL Server Enterprise Edition 时，虚拟机的最小建议大小是 A3。 当使用 SQL Server Enterprise Edition 时，请选择 A3 或更大。 当使用针对事务性工作负荷映像优化的 SQL Server 2012 或 2014 Enterprise 时，请选择 A4。
     > 当使用针对数据仓库工作负荷映像优化的 SQL Server 2012 或 2014 Enterprise 时，请选择 A7。 所选定的大小会限制您能够配置的数据磁盘个数。 有关可用虚拟机大小和可附加到虚拟机的数据磁盘数的最新信息，请参阅[用于 Azure 的虚拟机大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。 有关定价信息，请参阅[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)。
     > 
     > 
   
   单击右下角的“下一步”箭头以继续。
   
   ![VM 配置][2]
5. 在第二个“虚拟机配置”页上，配置网络、存储和可用性的资源：
   
   * 在“云服务”框中，选择“创建新云服务”。
   * 在“云服务 DNS 名称”框中，提供所选 DNS 名称的第一部分，使其按照 **TESTNAME.cloudapp.net** 格式完成名称
   * 在“区域/地缘组/虚拟网络”框中，选择将托管此虚拟映像的区域。
   * 在“存储帐户”中，选择现有存储帐户，或选择自动生成的存储帐户。
   * 在“可用性集”框中，选择“(无)”。
   * 阅读并接受定价信息。
6. 在“终结点”部分中，单击“名称”下的空下拉列表，选择“MSSQL”，然后键入数据库引擎实例的端口号（对于默认实例，端口号是**1433**）。
7. SQL Server VM 还可以充当 Python Notebook 服务器，这将在稍后的步骤中配置。
   添加新的终结点，以指定要用于 IPython Notebook 服务器的端口。 在“名称”列中输入名称，为公用端口选择所选的端口号，并为专用端口选择 9999。
   
   单击右下角的“下一步”箭头以继续。
   
   ![选择 MSSQL 和 IPython 端口][3]
8. 接受已选中的默认“安装 VM 代理”选项，单击向导右下角的复选标记以完成 VM 预配过程。
   
   `![VM 最终选项][4]
9. 请等待 Azure 准备虚拟机。 预计虚拟机状态如下：
   
   * 正在启动（正在配置）
   * 已停止
   * 正在启动（正在配置）
   * 正在运行（正在配置）
   * 正在运行

## <a name="a-nameremotedesktopaopen-the-virtual-machine-using-remote-desktop-and-complete-setup"></a><a name="RemoteDesktop"></a>使用远程桌面打开虚拟机并完成设置
1. 预配完成后，单击虚拟机名称转到“仪表板”页面。 在页面底部，单击“连接”。
2. 选择使用 Windows 远程桌面程序打开 rpd 文件 (`%windir%\system32\mstsc.exe`)。
3. 在“Windows 安全性”对话框中，提供你在前面步骤中指定的本地管理员帐户的密码。
   （系统可能会要求你验证虚拟机的凭据。）
4. 首次登录到此虚拟机时，可能需要完成若干过程，其中包括设置桌面、更新 Windows 和完成 Windows 初始配置任务 (sysprep)。 Windows sysprep 完成后，SQL Server 安装程序完成配置任务。 完成这些任务后可能会导致几分钟的延迟。 `SELECT @@SERVERNAME` 可能无法返回正确的名称，直到 SQL Server 安装完成，并且 SQL Server Management Studio 可能不会显示在起始页面上。

使用 Windows 远程桌面连接到虚拟机后，可以像使用任何其他计算机一样使用虚拟机。 以正常方式使用 SQL Server Management Studio（在虚拟机上运行）连接到 SQL Server 的默认实例。

## <a name="a-nameinstallipythonainstall-ipython-notebook-and-other-supporting-tools"></a><a name="InstallIPython"></a>安装 IPython Notebook 和其他支持工具
若要将新 SQL Server VM 配置为充当 IPython Notebook 服务器，并安装其他支持工具（例如 AzCopy、Azure 存储资源管理器、有用的数据科学 Python 包以及其他内容），系统将向你提供特殊的自定义脚本。 若要进行安装：

* 右键单击 Windows“开始”图标，然后单击“命令提示符(管理员)”
* 复制以下命令并粘贴到命令提示符下。
  
        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
* 当系统出现提示时，为 IPython Notebook 服务器输入所选的密码。
* 自定义的脚本自动执行多个安装后过程，其中包括：
  * 安装和设置 IPython Notebook 服务器
  * 在前面创建的终结点的 Windows 防火墙中打开 TCP 端口：
  * 用于 SQL Server 远程连接
  * 用于 IPython Notebook 服务器远程连接
  * 提取示例 IPython notebook 和 SQL 脚本
  * 下载和安装有用的数据科学 Python 包
  * 下载并安装 AzCopy 和 Azure 存储资源管理器之类的 Azure 工具  
    <br>
* 可以使用 `https://<virtual_machine_DNS_name>:<port>` 形式的 URL 从任何本地或远程浏览器访问并运行 IPython Notebook，其中的 port 是在预配虚拟机时所选择的 IPython 公用端口。
* IPython Notebook 服务器作为后台服务运行，并将在重新启动虚拟机时自动重新启动。

## <a name="a-nameoptionalaattach-data-disk-as-needed"></a><a name="Optional"></a>根据需要附加数据磁盘
如果 VM 映像不包含数据磁盘（即，C 驱动器（操作系统磁盘）和 D 驱动器（临时磁盘）之外的磁盘），需要添加一或多个数据磁盘来存储数据。 针对数据仓库工作负荷优化的 SQL Server 2012 SP2 Enterprise 的 VM 映像使用 SQL Server 数据和日志文件的附加磁盘进行预配置。

> [!NOTE]
> 不要使用 D 驱动器来存储数据。 顾名思义，它仅提供临时存储。 它不提供冗余或备份，因为它不驻留在 Azure 存储空间中。
> 
> 

若要附加数据磁盘，请按照[如何将数据磁盘附加到 Windows 虚拟机](../virtual-machines/virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)中所述的步骤操作，这将指导你完成：

1. 将空磁盘附加到之前步骤中预配的虚拟机
2. 虚拟机中新磁盘的初始化

## <a name="a-namessmsaconnect-to-sql-server-management-studio-and-enable-mixed-mode-authentication"></a><a name="SSMS"></a>连接到 SQL Server Management Studio 并启用混合模式身份验证
在没有域环境的情况下，SQL Server 数据库引擎无法使用 Windows 身份验证。 若要从其他计算机连接到数据库引擎，请将 SQL Server 的身份验证模式配置为混合。 混合模式身份验证同时允许 SQL Server 身份验证和 Windows 身份验证。 需要使用 SQL 身份验证模式，才能在 [Azure 机器学习工作室](https://studio.azureml.net)中通过“输入数据”模块直接从 SQL Server VM 数据库引入数据。

1. 在使用远程桌面连接到虚拟机时，使用 Windows“搜索”窗格，然后键入 **SQL Server Management Studio** (SMSS)。 单击以启动 SQL Server Management Studio (SSMS)。 可能需要在桌面上添加 SSMS 的快捷方式，供将来使用。
   
   ![启动 SSMS][5]
   
   Management Studio 在首次打开时，一定会创建用户 Management Studio 环境。 这可能需要一小段时间。
2. 打开时，Management Studio 会显示“连接到服务器”对话框。 在“服务器名称”框中，键入要连接到对象资源管理器中数据库引擎的虚拟机名称。
   （你还可使用“(local)”或一个句点代替虚拟机名称作为“服务器名称”。 选择“Windows 身份验证”，并保留“用户名”框中的“*your\_VM\_name*\\your\_local\_administrator”。 单击“连接”。
   
   ![连接到服务器][6]
   
   <br>
   
   > [!TIP]
   > 可以使用 Windows 注册表项更改，或使用 SQL Server Management Studio，来更改 SQL Server 身份验证模式。 若要使用注册表项更改来更改身份验证模式，请启动“新建查询”，然后执行以下脚本：
   > 
   > 
   
       USE master
       go
   
       EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
       go

    若要使用 SQL Server Management Studio 更改身份验证模式：

1. 在 SQL Server Management Studio 的“对象资源管理器”中，右键单击 SQL Server 实例的名称（虚拟机名称），然后单击“属性”。
   
   ![服务器属性][7]
2. 在“安全性”页上的“服务器身份验证”下，选择“SQL Server 和 Windows 身份验证模式”，然后单击“确定”。
   
   ![选择身份验证模式][8]
3. 在“SQL Server Management Studio”对话框中，单击“确定”接受重新启动 SQL Server 的要求。
4. 在“对象资源管理器”中，右键单击服务器，然后单击“重新启动”。 （如果 SQL Server 代理正在运行，它也必须重新启动。）
   
   ![重新启动][9]
5. 在“SQL Server Management Studio”对话框中，单击“是”同意重新启动 SQL Server。

## <a name="a-nameloginsacreate-sql-server-authentication-logins"></a><a name="Logins"></a>创建 SQL Server 身份验证登录名
若要从其他计算机连接到数据库引擎，你必须创建至少一个 SQL Server 身份验证登录名。  

可以采用编程方式或使用 SQL Server Management Studio，创建新的 SQL Server 登录名。 若要以编程方式使用 SQL 身份验证创建新的 sysadmin 用户，请启动“新建查询”并执行以下脚本。 将 <新用户名\> 和 <新密码\> 替换为所选的*用户名*和*密码*。 

    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


根据需要调整密码策略（示例代码行会关闭策略检查和密码过期）。 有关 SQL Server 登录名的详细信息，请参阅[创建登录名](http://msdn.microsoft.com/library/aa337562.aspx)。  

若要使用 SQL Server Management Studio 创建新的 SQL Server 登录名：

1. 在 SQL Server Management Studio 的“对象资源管理器”中，展开要在其中创建新登录名的服务器实例所在的文件夹。
2. 右键单击“安全性”文件夹、指向“新建”，然后选择“登录名…”。
   
   ![新建登录名][10]
3. 在“登录名 - 新建”对话框中的“常规”页上，在“登录名”框中输入新用户的名称。
4. 选择“SQL Server 身份验证”。
5. 在“密码”框中，输入新用户的密码。 在“确认密码”框中再次输入该密码。
6. 若要强制实施针对复杂性和强制实施的密码策略选项，请选择“强制实施密码策略”（推荐）。 这是选择 SQL Server 身份验证时的默认选项。
7. 若要强制实施针对过期的密码策略选项，请选择“强制密码过期”（推荐）。 必须选择强制密码策略才能启用此复选框。 这是选择 SQL Server 身份验证时的默认选项。
8. 若要强制用户在首次使用登录名后创建新密码，请选择“用户在下次登录时必须更改密码”（如果此登录名给其他人使用，推荐选择此选项。 如果登录名是为了自用，请勿选择此选项。）必须选择强制密码过期才能启用此复选框。 这是选择 SQL Server 身份验证时的默认选项。
9. 从“默认数据库”列表中，为该登录名选择默认数据库。 “master”是此选项的默认值。 如果尚未创建用户数据库，则保留此设置为“master”。
10. 在“默认语言”列表中，保留“默认”值。
    
    ![登录名属性][11]
11. 如果这是你创建的第一个登录名，可能会需要将此登录名指派为 SQL Server 管理员。 这样的话，请在“服务器角色”页面上选中“sysadmin”。
    
    > [!IMPORTANT]
    > sysadmin 固定服务器角色的成员对数据库引擎具有完全控制权限。 出于安全原因，应谨慎限制此角色中的成员资格。
    > 
    > 
    
    ![sysadmin][12]
12. 单击“确定”。

## <a name="a-namednsadetermine-the-dns-name-of-the-virtual-machine"></a><a name="DNS"></a>确定虚拟机的 DNS 名称
若要从另一台计算机连接到 SQL Server 数据库引擎，必须知道虚拟机的域名系统 (DNS) 名称。

（这是 Internet 用于识别虚拟机的名称）。 可以使用 IP 地址，但 IP 地址在 Azure 为冗余或维护而移动资源时可能会变更。 DNS 名称将保持不变，因为可将该名称重定向到新的 IP 地址。）

1. 在 Azure 经典门户中（或在完成上一步后），选择“虚拟机”。
2. 在“虚拟机实例”页上的“DNS 名称”列中，找到并复制带有前缀“http://”的虚拟机 DNS 名称。 （在用户界面上可能显示不出整个名称，不过没关系，你可以右键单击它，并选择“复制”。）

## <a name="a-namecdeaconnect-to-the-database-engine-from-another-computer"></a><a name="cde"></a>从其他计算机连接到数据库引擎
1. 在连接到 Internet 的计算机上，打开 SQL Server Management Studio。
2. 在“连接到服务器”或“连接到数据库引擎”对话框的“服务器名称”框中，输入虚拟机的 DNS 名称（在以前的任务中确定）和 *DNSName,portnumber* 格式的公共终结点端口号（例如 **tutorialtestVM.cloudapp.net,57500**）。
3. 在“身份验证”框中，选择“SQL Server 身份验证”。
4. 在“登录名”框中，键入在前面的任务中创建的登录名。
5. 在“密码”框中，键入在前面的任务中创建的登录名的密码。
6. 单击“连接”。

## <a name="a-nameamlconnectaconnect-to-the-database-engine-from-azure-machine-learning"></a><a name="amlconnect"></a>从 Azure 机器学习连接到数据库引擎
在团队数据科学过程的后期，将使用 [Azure 机器学习工作室](https://studio.azureml.net)生成和部署机器学习模型。 若要将数据直接从 SQL Server VM 数据库引入 Azure 机器学习以供训练或评分，则在新的 [Azure 机器学习工作室](https://studio.azureml.net)实验中使用“导入数据”模块。 将通过数据科学过程链接详细介绍本主题。 有关说明，请参阅[什么是 Azure 机器学习工作室？](machine-learning-what-is-ml-studio.md)。

1. 在[“导入数据”模块](https://msdn.microsoft.com/library/azure/dn905997.aspx)的“属性”窗格中，从“数据源”下拉列表中选择“Azure SQL 数据库”。
2. 在“数据库名称”框中，输入 `tcp:<DNS name of your virtual machine>,1433`
3. 在“服务器用户帐户名”文本框中输入 SQL 用户名。
4. 在“服务器用户帐户密码”文本框中输入 SQL 用户密码。
   
   ![Azure ML 导入数据][13]

## <a name="a-nameshutdownashutdown-and-deallocate-virtual-machine-when-not-in-use"></a><a name="shutdown"></a>关闭并解除分配未使用的虚拟机
Azure 虚拟机定价为**只为自己使用的东西付费**。 若要确保未使用虚拟机时不会计费，它必须处于“已停止(已解除分配)”状态。

> [!NOTE]
> 如果从 VM 关闭虚拟机（使用 Windows 电源选项），则 VM 已停止，但仍处于分配状态。 要确保不被计费，请始终从 [Azure 经典门户](http://manage.windowsazure.com/)停止虚拟机。 也可以通过 Powershell 停止 VM，方法是调用 ShutdownRoleOperation 并使“PostShutdownAction”等于“StoppedDeallocated”。
> 
> 

若要关闭并解除分配虚拟机：

1. 使用帐户登录到 [Azure 经典门户](http://manage.windowsazure.com/)。  
2. 从左侧导航栏选择“虚拟机”。
3. 在虚拟机列表中，单击虚拟机的名称，然后转到“仪表板”页。
4. 在页面底部，单击“关闭”。

![VM 关闭][15]

虚拟机将解除分配，但不会删除。 可以随时从 Azure 经典门户重新启动虚拟机。

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>Azure SQL Server VM 可随时使用：后续步骤是什么？
现在，虚拟机可随时在数据科学练习中使用。 虚拟机还可以用作 IPython Notebook 服务器来浏览和处理数据，以及其他可与 Azure 机器学习和团队数据科学过程 (TDSP) 一起执行的任务。

团队数据科学过程中的后续步骤映射到[团队数据科学过程](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中，可能包括将数据移动到 HDInsight、处理并在其中进行采样的步骤，以便为使用 Azure 机器学习来了解数据做好准备。

[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png




<!--HONumber=Nov16_HO3-->


