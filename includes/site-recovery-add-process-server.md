1. 启动 Azure 站点恢复 UnifiedSetup.exe
2. 在**在开始之前**，选择**添加要向外扩展部署的其他进程服务器**。

  ![添加进程服务器](./media/site-recovery-add-process-server/ps-page-1.png)

3. 在**配置服务器详细信息**，指定的 IP 地址的配置服务器，以及的密码。

  ![添加进程服务器 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. 在**Internet 设置**，指定如何配置服务器上运行的提供程序连接到 Azure Site Recovery 通过 Internet。

  ![添加进程服务器 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * 如果你想要使用的代理的当前设置的计算机上，选择连接**使用现有的代理设置连接**。
   * 如果你想要直接连接的提供程序，选择**不使用代理直接连接**。
   * 如果现有的代理要求身份验证，或如果你想要使用自定义代理进行选择的提供程序连接**使用自定义代理设置连接**。

     * 如果你使用自定义代理，你需要指定地址、 端口和凭据。
     * 如果你使用的代理，你应具有已允许访问的服务 url。

5. 在**必备项检查**，安装程序运行检查，以确保安装可以运行。 如果出现有关的警告**世界时间同步检查**，验证系统时钟上的时间 (**日期和时间**设置) 与时区相同。

     ![添加进程服务器 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. 在**环境详细信息**，选择是否要复制 VMware 虚拟机。 如果你是，然后安装程序将检查安装了 PowerCLI 6.0。

     ![添加进程服务器 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. 在**安装位置**，选择你想要安装二进制文件和存储缓存的位置。 你选择的驱动器必须具有至少 5 GB 的可用磁盘空间，但我们建议在至少具有 600 GB 的可用空间的缓存驱动器。
     ![添加进程服务器 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. 在**网络选择**，指定的侦听器 （网络适配器和 SSL 端口） 在其配置服务器发送和接收复制数据。 端口 9443 是用来发送和接收复制流量的默认端口，但可以修改此端口号，以满足你的环境的要求。 除了端口 9443，我们还会打开端口 443，web 服务器用于安排复制操作。 不要使用端口 443 来发送或接收复制流量。

     ![添加进程服务器 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. 在**摘要**，查看信息，请单击**安装**。 当安装完成后时，会生成一个通行短语。 当你启用复制，因此将其复制并将其保存在安全位置，你将需要此。

     ![添加进程服务器 7](./media/site-recovery-add-process-server/ps-page-8.png)
