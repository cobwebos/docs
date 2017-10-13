<!--author=jgerend last changed: 03/16/16-->

## <a name="preparing-for-updates"></a>准备更新
在扫描和应用更新之前需要执行以下步骤：

1. 创建设备数据的云快照。
2. 确保控制器的固定 IP 可路由，并且可以连接到 Internet。 这些固定 IP 用于提供设备更新。 可以通过设备的 Windows PowerShell 界面，在每个控制器上运行以下 cmdlet 来测试 IP：
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
   
    **当固定 IP 可连接到 Internet 时 Test-Connection 的示例输出**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

在成功完成这些手动预先检查之后，便可以继续扫描和安装更新。

