## <a name="download-install-and-register-the-azure-backup-agent"></a>下载、 安装和注册 Azure 备份代理
在创建 Azure 备份保管库后, 应在每台 Windows 计算机 （Windows Server、 Windows 客户端、 System Center Data Protection Manager 服务器或 Azure 备份服务器计算机），使备份的数据和应用程序到 Azure 上安装代理。

1. 登录到[管理门户](https://manage.windowsazure.com/)
2. 单击**恢复服务**，然后选择你想要注册到服务器的备份保管库。 将显示该备份保管库的快速启动页。
   
    ![快速入门](./media/backup-install-agent/quickstart.png)
3. 在快速启动页上，单击**For Windows Server 或 System Center Data Protection Manager 或 Windows 客户端**选项下**下载代理**。 单击**保存**将其复制到本地计算机。
   
    ![保存代理](./media/backup-install-agent/agent.png)
4. 安装代理后，双击 MARSAgentInstaller.exe 以启动 Azure 备份代理的安装。 选择安装文件夹和代理所需的临时文件夹。 指定的缓存位置必须具有至少 5%的备份数据可用空间。
5. 如果使用代理服务器连接到 internet，请在**代理配置**屏幕中，输入代理服务器详细信息。 如果你使用经过身份验证的代理，请在此屏幕中输入用户名称和密码详细信息。
6. Azure 备份代理安装.NET Framework 4.5 和 Windows PowerShell （如果还没有可用的话） 以完成安装。
7. 安装代理后，单击**转到注册**按钮以继续运行工作流。
   
   ![注册](./media/backup-install-agent/register.png)
8. 在保管库凭据屏幕中，浏览到并选择前面下载的保管库凭据文件。
   
    ![保管库凭据](./media/backup-install-agent/vc.png)
   
    保管库凭据文件是只能生效 48 小时 （从门户下载） 后。 如果您遇到此屏幕 （例如"保管库凭据文件提供已过期"），登录到 Azure 门户中的任何错误，并再次下载保管库凭据文件。
   
    确保保管库凭据文件在安装应用程序可以访问的位置中可用。 如果你遇到访问相关的错误，将保管库凭据文件复制到此计算机中的临时位置并重试该操作。
   
    如果您遇到无效的保管库凭据错误 （例如"提供无效的保管库凭据"） 文件已损坏或者没有最新的凭据关联与恢复服务。 重试该操作后从门户下载新的保管库凭据文件。 如果用户单击，则通常会出现此错误**下载保管库凭据**在 Azure 门户中快速连续的选项。 在这种情况下，只有第二个保管库凭据文件是有效的。
9. 在**加密设置**屏幕上，你可以生成一个通行短语，或者可以提供一个通行短语 （最少包含 16 个字符）。 请记住将通行短语保存在安全位置。
   
    ![加密](./media/backup-install-agent/encryption.png)
   
   > [!WARNING]
   > 如果丢失或忘记; 密码Microsoft 无法帮助恢复备份的数据。 最终用户拥有的加密通行短语，Microsoft 不具有可见性由最终用户的密码。 请请将文件保存在安全位置在需要时恢复操作过程。
   > 
   > 
10. 单击后**完成**按钮，计算机成功注册到保管库，你现在可以开始备份到 Microsoft Azure。
11. 使用 Microsoft Azure 备份独立时你可以修改通过单击注册工作流期间指定的设置**更改属性**在 Azure 备份 mmc 管理单元中的选项。
    
    ![更改属性](./media/backup-install-agent/change.png)
    
    或者，在使用 Data Protection Manager，你可以修改通过单击注册工作流期间指定的设置**配置**通过选择的选项**联机**下**管理**选项卡。
    
    ![配置 Azure 备份](./media/backup-install-agent/configure.png)

