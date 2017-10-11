### <a name="determine-the-dns-name-of-the-virtual-machine"></a>确定虚拟机的 DNS 名称
若要从另一台计算机连接到 SQL Server 数据库引擎，必须知道虚拟机的域名系统 (DNS) 名称。 （这是 internet 用于识别虚拟机的名称。 你可以使用的 IP 地址，但 Azure 移动资源以实现冗余或进行维护时，可能会更改的 IP 地址。 DNS 名称将不变，因为它可以重定向到新的 IP 地址。）  

1. 在 Azure 门户中 （或从上一步），选择**虚拟机 （经典）**。
2. 选择你的 SQL VM。
3. 上**虚拟机**边栏选项卡中，复制**DNS 名称**为虚拟机。
   
    ![DNS 名称](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>从另一台计算机连接到数据库引擎
1. 在计算机连接到 internet 上，打开 SQL Server Management Studio。
2. 在**连接到服务器**或**连接到数据库引擎**对话框中，在**服务器名称**框中，输入虚拟机 （在上一任务中确定） 和公共终结点端口号的 DNS 名称的格式*DNSName，portnumber*如**mysqlvm.cloudapp.net,57500**。
   
    ![使用 SSMS 进行连接](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    如果你不记得公共终结点端口号之前创建，你可以找到它在**终结点**区域**虚拟机**边栏选项卡。
   
    ![公用端口](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. 在**身份验证**框中，选择**SQL Server 身份验证**。
4. 在**登录**框中，键入你在前一任务中创建的登录名的名称。
5. 在**密码**框中，键入你在前一任务中创建登录名的密码。
6. 单击“连接” 。

