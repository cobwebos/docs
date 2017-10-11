* 在**添加 vCenter**，指定 vSphere 主机或 vCenter 服务器的友好名称，然后指定的 IP 地址或服务器的 FQDN。 除非将 VMware 服务器配置为在不同的端口上侦听请求，请保留端口为 443。 选择要连接到 VMware vCenter 或 vSphere ESXi 服务器的帐户。 单击" **确定**"。

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > 如果你要添加 VMware vCenter 服务器或 VMware vSphere 主机不在 vCenter 或主机服务器上具有管理员特权的帐户，请确保帐户具有启用这些特权： 分布式数据中心、 数据存储、 文件夹、 主机、 网络、 资源、 虚拟机和 vSphere 切换。 此外，VMware vCenter 服务器需要启用了存储视图特权。
