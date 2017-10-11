## <a name="set-up-the-development-environment"></a>设置开发环境

本部分将指导你设置开发环境，包括创建 ASP.NET MVC 应用程序、 添加连接服务连接、 添加控制器，和指定所需的命名空间指令。

### <a name="create-an-aspnet-mvc-app-project"></a>创建 ASP.NET MVC 应用程序项目

1. 打开 Visual Studio。

1. 选择**文件-> 新建-> 项目**从主菜单

1. 上**新项目**对话框中，将选项指定为在下图中突出显示：

    ![创建 ASP.NET 项目](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. 选择**确定**。

1. 上**新建 ASP.NET 项目**对话框中，将选项指定为在下图中突出显示：

    ![指定 MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. 选择**确定**。

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>使用连接服务连接到 Azure 存储帐户

1. 在**解决方案资源管理器**，右键单击项目，并从上下文菜单中，选择**添加-> 连接的服务**。

1. 上**添加连接的服务**对话框中，选择**Azure 存储空间**，然后选择**配置**。

    ![连接的服务对话框](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. 上**Azure 存储空间**对话框中，选择你要与之正常工作，并选择所需的 Azure 存储帐户**添加**。
