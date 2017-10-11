

当 Azure 创建的 web 应用程序项目时，你可以设置 Azure 中的虚拟机。 然后可以使用其他软件，配置虚拟机或虚拟机用于诊断或调试。

若要创建 web 应用程序时，请创建虚拟机，请按照下列步骤：

1. 在 Visual Studio 中，单击**文件** > **新建** > **项目** > **Web**，然后选择**ASP.NET Web 应用程序**(下**Visual C#**或**Visual Basic**节点)。
2. 在**新建 ASP.NET 项目**对话框中，选择所需的 web 应用程序的类型，并在对话框中 （位于右下角中） 的 Azure 部分中，确保**在云中托管**选中复选框 (此复选框标记为**创建远程资源**在某些安装)。
   
    ![][0]
3. 对于此示例中，在 Microsoft Azure 下的下拉列表选择**虚拟机 (v1)**，然后单击**确定**按钮。
4. 如果系统提示你，登录到 Azure。 **创建虚拟机**对话框随即出现。
   
    ![][2]
5. 在**DNS 名称**框中，输入虚拟机的名称。 DNS 名称必须是在 Azure 中唯一的。 如果您输入的名称不可用，将出现一个红色感叹号。
6. 在**映像**列表中，选择你想要用作虚拟机基础的映像。 你可以选择任何标准 Azure 虚拟机映像，或者你已上载到 Azure 的映像。
7. 保留**启用 IIS 和 Web 部署**选中，除非你打算安装其他 web 服务器的复选框。 你将无法从 Visual Studio 发布，如果您禁用 Web 部署。 你可以将 IIS 和 Web 部署添加到任何打包的 Windows Server 映像，包括你自己的自定义映像。
8. 在**大小**列表中，选择虚拟机的大小。
9. 指定此虚拟机的登录凭据。 请记下这些信息，因为你将需要它们以通过远程桌面访问计算机。
10. 在**位置**列表中，选择要承载虚拟机的区域。
11. 单击**确定**按钮开始创建虚拟机。 你可以按照中的操作的进度**输出**窗口。
    
    ![][3]
12. 设置虚拟机时，在创建发布的脚本**PublishScripts**解决方案中的节点。 已发布的脚本运行，并设置 Azure 中的虚拟机。 **输出**窗口将显示状态。 脚本将执行以下操作来设置虚拟机：
    
    * 如果它尚不存在，请创建虚拟机。
    * 开头的名称创建存储帐户`devtest`，但仅当指定的区域中没有这样一个存储帐户。
    * 作为虚拟机的容器中创建云服务，并创建 web 应用程序的 web 角色。
    * 配置虚拟机上的 Web 部署。
    * 虚拟机上配置 IIS 和 ASP.NET。
    
    ![][4]
13. （可选）你可以连接到新的虚拟机。 在**服务器资源管理器**，展开**虚拟机**节点，选择虚拟机创建，且其快捷菜单上的节点，选择**使用远程桌面连接**。 或者，在**云资源管理器**可以选择**在门户中打开**上的快捷菜单，然后连接到那里的虚拟机。
    
    ![][5]

## <a name="next-steps"></a>后续步骤
如果你想要自定义所创建的已发布的脚本，阅读更深入的信息[发布到开发和测试环境中使用 Windows PowerShell 脚本](http://msdn.microsoft.com/library/dn642480.aspx)。

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
