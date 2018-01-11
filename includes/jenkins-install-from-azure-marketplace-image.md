1. 在浏览器中，打开[适用于 Jenkins 的 Azure Marketplace 映像](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)。

1. 选择“立即获取”。

    ![选择“立即获取”可启动 Jenkins Marketplace 映像的安装过程。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. 在查看定价详细信息和条款信息后，选择“继续”。

    ![Jenkins Marketplace 映像的定价和条款信息。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. 选择“创建”可在 Azure 门户中配置 Jenkins 服务器。 

    ![安装 Jenkins Marketplace 映像。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. 在“基本”选项卡上，指定以下值：

    - **名称** - 输入“`Jenkins`”。
    - **用户** - 输入登录到运行 Jenkins 的虚拟机时要使用的用户名。
    - **身份验证类型** - 选择“密码”。
    - **密码** - 输入登录到运行 Jenkins 的虚拟机时要使用的密码。
    - **确认密码** - 重新输入登录到运行 Jenkins 的虚拟机时要使用的密码。
    - **Jenkins 发行类型** - 选择 **LTS**。
    - **订阅** - 选择要用于安装 Jenkins 的 Azure 订阅。
    - **资源组** - 选择“新建”，并输入资源组的名称，该资源组用作构成 Jenkins 安装的资源集合的逻辑容器。
    - **位置** - 选择“美国东部”。

    ![在“基本”选项卡中输入 Jenkins 的身份验证和资源组信息。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. 选择“确定”以进入“设置”选项卡。 

1. 在“设置”选项卡中，指定以下值：

    - **大小** - 选择适合于 Jenkins 虚拟机的调整大小选项。
    - **VM 磁盘类型** - 指定 HDD（硬盘驱动器）或 SSD（固态驱动器）以指明允许将哪种存储磁盘类型用于 Jenkins 虚拟机。
    - **公共 IP 地址** - IP 地址名称默认为在前一页中指定的带有 -IP 后缀的 Jenkins 名称。 可以选择相应选项更改该默认设置。
    - **域名标签** - 为 Jenkins 虚拟机的完全限定 URL 指定值。

    ![在“设置”选项卡中输入适用于 Jenkins 的虚拟机设置。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. 选择“确定”以进入“摘要”选项卡。

1. 显示“摘要”选项卡时，将验证输入的信息。 看到“验证通过”消息后，选择“确定”。 

    ![“摘要”选项卡显示并验证所选的选项。](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. 显示“创建”选项卡时，选择“创建”以创建 Jenkins 虚拟机。 服务器就绪时，将在 Azure 门户中显示一条通知。

    ![Jenkins 就绪通知。](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)