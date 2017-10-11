<!--author=jgerend last changed: 03/16/16-->

## <a name="preparing-for-updates"></a>准备更新
你将需要扫描并应用更新之前执行以下步骤：

1. 需要将设备的数据的云快照。
2. 确保你的控制器固定 Ip 可路由，并且可以连接到 Internet。 这些固定的 Ip 用于服务更新你的设备。 你可以通过从设备的 Windows PowerShell 接口在每个控制器上运行以下 cmdlet 来测试此：
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
   
    **测试连接时固定的 Ip 可以连接到 Internet 的输出示例**

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

您已经成功完成这些手动预检查后，你可以继续扫描和安装更新。

