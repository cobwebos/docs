
1. 在**混合连接**边栏选项卡，单击你刚的混合连接创建，然后单击**侦听器安装**。
   
    ![单击侦听器安装](./media/app-service-hybrid-connections-manager-install/D04ClickListenerSetup.png)
2. **混合连接属性**边栏选项卡将打开。 下**本地混合连接管理器**，选择**下载并手动配置**，保存下载的 HybridConnectionManager.msi 包，并复制网关连接字符串。
   
    ![单击此处安装](./media/app-service-hybrid-connections-manager-install/D05ClickToInstallHCM.png)
3. 在管理员命令提示符下键入以下命令以启动安装程序：
   
        start HybridConnectionManager.msi
4. 安装程序运行后，单击**现在不**、 然后浏览到 %ProgramFiles%\Microsoft\HybridConnectionManager 文件夹、 运行 HCMConfigWizard.exe 和单击**是**中**用户帐户控制**对话框。
5. 粘贴先前复制的混合连接字符串，然后单击**确定**。 
   
    ![安装](./media/app-service-hybrid-connections-manager-install/D08aHCMInstallManual.png)
6. 安装完成后，单击**关闭**。
   
    ![单击关闭](./media/app-service-hybrid-connections-manager-install/D09HCMInstallComplete.png)
   
    上**混合连接**边栏选项卡，**状态**列现在显示**已连接**。 
   
    ![连接的状态](./media/app-service-hybrid-connections-manager-install/D10HCStatusConnected.png)

