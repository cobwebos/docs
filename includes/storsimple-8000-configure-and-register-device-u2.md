<!--author=alkohli last changed: 01/18/2017-->


#### <a name="to-configure-and-register-the-device"></a>若要配置和注册设备

1. 访问在你的 StorSimple 设备串行控制台上的 Windows PowerShell 接口。 请参阅[使用 PuTTY 连接到设备串行控制台](#use-putty-to-connect-to-the-device-serial-console)有关的说明。 **请务必严格遵照该过程或你将无法访问控制台。**

2. 在打开的会话，按**Enter**一次，以启动命令提示符。

3. 系统将提示你选择的语言，你想要为你设备设置。 指定的语言，然后按**Enter**。

4. 在显示串行控制台菜单中，选择选项 1 到**完全访问权限登录**。
     完成步骤 5-12 以配置你的设备的最小所需的网络设置。 **这些配置步骤需要设备在活动控制器上执行。** 串行控制台菜单中指示横幅消息中的控制器状态。 如果你未连接到主动控制器，断开连接，然后连接到主动控制器。

5. 在命令提示符处，键入你的密码。 默认设备密码**Password1**。

6. 键入以下命令： `Invoke-HcsSetupWizard`。

7. 安装向导将显示帮助你配置设备的网络设置。 提供以下信息：
   
   * DATA 0 网络接口的 IP 地址
   * 子网掩码
   * Gateway
   * 主 DNS 服务器的 IP 地址

   下面显示了示例输出。

    ```
        ---------------------------------------------------------------
        Microsoft Azure StorSimple Appliance Model 8100
        Name: 8100-SHX0991003G44MT
        Software Version: 6.3.9600.17759
        Copyright (C) 2014 Microsoft Corporation. All rights reserved.
        You are connected to Controller0 - Active
        ---------------------------------------------------------------

        Your device needs to be registered with the Microsoft Azure StorSimple Manager service. Please run 'Invoke-HcsSetupWizard' to set up your device.

        Controller0>Invoke-HcsSetupWizard

        Which IP address family would you like to configure on interface Data0?
        [4] IPv4 [6] IPv6 [B] Both (Default is "4"): 4

        Data0 IPv4 address:10.111.111.00
        Data0 IPv4 subnet: 255.255.252.0
        Data0 IPv4 gateway: 10.111.111.11

        IPv4 primary DNS server [10.222.118.154]:10.222.222.111
    ```

    <br>
    在前面的示例输出中，你可以查看系统在过程中每个步骤后验证网络设置。

     > [!NOTE]
     > 你可能需要等待几分钟让子网掩码和要应用的 DNS 设置。 如果你收到"检查 Data 0 的网络连接"错误消息，请检查主动控制器 DATA 0 网络接口上的物理网络连接。

8. （可选） 配置 web 代理服务器。 尽管 web 代理配置是可选的但**请注意，如果你使用 web 代理，你可以仅配置它此处**。 有关详细信息，请转到[配置你的设备的 web 代理](../articles/storsimple/storsimple-8000-configure-web-proxy.md)。
9. 配置你的设备的主 NTP 服务器。 你的设备必须同步时间，以便它可以使用云服务提供商进行验证是必需的 NTP 服务器。 确保你的网络允许 NTP 流量从你的数据中心传递到 Internet。 如果无法做到这一点，指定一个内部的 NTP 服务器。

    示例输出所示。

    ```
        Would you like to configure a web proxy?
        [Y] Yes [N] No (Default is "N"):N

        Primary NTP server [time.windows.com]:time.windows.com

    ```

10. 出于安全原因，设备管理员密码过期后的第一个会话，并将需要立即更改。 出现提示时，提供设备管理员密码。 有效的设备管理员密码必须介于 8 到 15 个字符之间。 密码必须包含的以下三种： 小写、 大写、 数字和特殊字符。

    ```
        The device administrator password must be between 8 and 15 characters. The password must contain a combination of uppercase letters, lowercase letters, numbers and special characters.
        Administrator Password:********
        Confirm Administrator Password:********
    ```
11. 安装向导中的最后一步使用 StorSimple 设备管理器服务注册你的设备。 为此，你将需要在步骤 2 中获取服务注册密钥。 提供注册密钥后，你可能需要等待 2-3 分钟，然后注册该设备。
    
    > [!NOTE]
    > 可以随时可以退出安装向导中按 Ctrl + C。 如果您输入的所有网络设置 （Data 0、 子网掩码和网关的 IP 地址），将保留你的条目。
    
    示例输出所示。

    ```
        The service registration key is available in the StorSimple Manager service.
        Enter service registration key:**************************************
        Device registration is in progress. Please wait.

    ```

12. 注册设备之后，将显示一个服务数据加密密钥。 复制此密钥并将其保存在安全的位置。 **与用于其他设备注册到 StorSimple 设备管理器服务的服务注册密钥将需要使用此密钥。** 请参阅[StorSimple 安全性](../articles/storsimple/storsimple-security.md)有关此密钥的详细信息。
    
    ![StorSimple 注册设备 7](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup1.png)
    
    > [!NOTE]
    > 若要从串行控制台窗口复制文本，只需选择的文本。 你应能够将其粘贴到剪贴板或任何文本编辑器中。 请勿使用 Ctrl + C 复制服务数据加密密钥。 使用 Ctrl + C 将导致你退出安装向导。 因此，将不会更改设备管理员密码，设备将恢复到默认密码。
    
13. 退出串行控制台。
14. 返回到 Azure 门户中，并完成以下步骤：
    
    1. 请转到你的 StorSimple 设备管理器服务。
    2. 单击“设备” 。
    3. 在设备的表格式列表中，验证设备成功连接到该服务通过查看状态。 设备状态应该是**已准备好设置**。
       
        ![StorSimple 设备页](./media/storsimple-8000-configure-and-register-device-u2/step3pssetup2.png)
       
        你可能需要等待几分钟让设备状态将更改为**已准备好设置**。
       
        如果设备未显示在此列表中，则需要确保你的防火墙网络已配置中所述[StorSimple 设备的网络要求](../articles/storsimple/storsimple-8000-system-requirements.md)。 验证端口 9354 打开用于出站通信，因为这用于 service bus StorSimple 设备管理器服务到设备通信。

