### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>在 Windows 防火墙中为数据库引擎的默认实例打开 TCP 端口
1. 通过远程桌面连接到虚拟机。 有关连接到 VM 的详细说明，请参阅[使用远程桌面打开 SQL VM](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#remotedesktop)。
2. 登录后，在开始屏幕中，键入“WF.msc”，并按 ENTER。
   
    ![启动防火墙程序](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. 在“高级安全 Windows 防火墙”的左窗格中，右键单击“入站规则”，并在操作窗格中单击“新建规则”。
   
    ![新建规则](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. 在“新建入站规则向导”对话框中，在“规则类型”下，选择“端口”，并单击“下一步”。
5. 在“协议和端口”对话框中，使用默认“TCP”。 然后，在“特定本地端口”框中，键入数据库引擎实例的端口号（即默认实例对应的端口号“1433”，或在终结点步骤中为专用端口选择的端口号）。
   
    ![TCP 端口 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“操作”对话框中，选择“允许连接”，并单击“下一步”。
   
    **安全说明：**选择“只允许安全连接”可增加安全性。 如果想在环境中配置其他安全性选项，请选择此选项。
   
    ![允许连接](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. 在“配置文件”对话框中，选择“公用”、“专用”和“域”。 然后单击“下一步”。
   
    **安全说明：**选择“公用”允许通过 Internet 进行访问。 只要有可能，就请选择更具限制性的配置文件。
   
    ![公用配置文件](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. 在“名称”对话框中，键入此规则的名称和说明，并单击“完成”。
   
    ![规则名称](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

根据需要为其他组件打开附加端口。 有关详细信息，请参阅[配置 Windows 防火墙以允许 SQL Server 访问](http://msdn.microsoft.com/library/cc646023.aspx)。

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>将 SQL Server 配置为侦听 TCP 协议

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>配置混合模式的 SQL Server 身份验证
在没有域环境的情况下，SQL Server 数据库引擎无法使用 Windows 身份验证。 要从其他计算机连接到数据库引擎，请将 SQL Server 的身份验证模式配置为混合。 混合模式身份验证同时允许 SQL Server 身份验证和 Windows 身份验证。

> [!NOTE]
> 如果已使用配置的域环境配置了 Azure 虚拟网络，可能没有必要配置混合模式身份验证。
> 
> 

1. 在连接到虚拟机时，在“开始”页面中，键入“SQL Server Management Studio”，并单击勾选图标。
   
    Management Studio 在首次打开时，一定会创建用户 Management Studio 环境。 这可能需要一小段时间。
2. Management Studio 会显示“连接到服务器”对话框。 在“服务器名称”框中键入要使用对象资源管理器连接到数据库引擎的虚拟机的名称（除了虚拟机名称，还可以使用“(local)”或一个句点作为“服务器名称”）。 选择“Windows 身份验证”，在“用户名”框中保留 ***your_VM_name*\your_local_administrator**。 单击“连接”。
   
    ![连接到服务器](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. 在 SQL Server Management Studio 的“对象资源管理器”中，右键单击 SQL Server 实例的名称（虚拟机名称），并单击“属性”。
   
    ![服务器属性](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. 在“安全性”页上的“服务器身份验证”下，选择“SQL Server 和 Windows 身份验证模式”，并单击“确定”。
   
    ![选择身份验证模式](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. 在 SQL Server Management Studio 对话框中，单击“确定”接受重新启动 SQL Server 的要求。
6. 在“对象资源管理器”中，右键单击服务器，并单击“重新启动”。 （如果 SQL Server 代理正在运行，它也必须重新启动。）
   
    ![重新启动](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. 在 SQL Server Management Studio 对话框中，单击“是”同意重新启动 SQL Server。

### <a name="create-sql-server-authentication-logins"></a>创建 SQL Server 身份验证登录名
要从其他计算机连接到数据库引擎，必须创建至少一个 SQL Server 身份验证登录名。

1. 在 SQL Server Management Studio 对象资源管理器中，展开要在其中创建新登录名的服务器实例所在的文件夹。
2. 右键单击“安全性”文件夹，指向“新建”，并选择“登录名…”。
   
    ![新建登录名](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. 在“登录名 - 新建”对话框中的“常规”页上，在“登录名”框中输入新用户的名称。
4. 选择“SQL Server 身份验证”。
5. 在“密码”框中，输入新用户的密码。 在“确认密码”框中再次输入该密码。
6. 选择所需的密码强制选项（“强制实施密码策略”、“强制密码过期”和“用户在下次登录时必须更改密码”）。 如果使用此登录名，则无需在下次登录时更改密码。
7. 从“默认数据库”列表中，为该登录名选择默认数据库。 “master”是此选项的默认值。 如果尚未创建用户数据库，则保留此设置为“master”。
   
    ![登录名属性](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. 如果这是你创建的第一个登录名，可能会需要将此登录名指派为 SQL Server 管理员。 这样的话，请在“服务器角色”页面上选中“sysadmin”。
   
   > [!NOTE]
   > sysadmin 固定服务器角色的成员对数据库引擎具有完全控制权限。 应谨慎限制此角色中的成员资格。
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. 单击“确定”。

有关 SQL Server 登录名的详细信息，请参阅[创建登录名](http://msdn.microsoft.com/library/aa337562.aspx)。

