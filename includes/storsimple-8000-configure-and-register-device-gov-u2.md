<!--author=SharS last changed: 06/22/2016-->

### <a name="to-configure-and-register-the-device"></a>配置和注册设备
1. 访问 StorSimple 设备串行控制台上的 Windows PowerShell 接口。 有关说明，请参阅 [使用 PuTTY 连接到设备串行控制台](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) 。 **请务必严格遵照该步骤，否则将无法访问控制台。**
2. 在打开的会话中，按一次 **Enter** 以启动命令提示符。
3. 系统会提示选择要为设备设置的语言。 指定语言，并按 **Enter**。
   
    ![StorSimple 配置和注册设备 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. 在显示的串行控制台菜单中，选择选项 1 以具有完全访问权限进行登录。
   
    ![StorSimple 注册设备 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. 执行以下步骤，为设备配置最低要求的网络设置。
   
   > [!IMPORTANT]
   > 需要在该设备的活动控制器上执行这些配置步骤。 串行控制台菜单指示标题消息中的控制器状态。 如果未连接到活动控制器，请断开连接，并连接到活动控制器。
   
   1. 在命令提示符处，键入密码。 默认设备密码为 **Password1**。
   2. 输入以下命令：
      
        `Invoke-HcsSetupWizard`
   3. 随后将出现安装向导，以帮助你配置设备的网络设置。 提供以下信息：
      
      * DATA 0 网络接口的 IP 地址
      * 子网掩码
      * 网关
      * 主 DNS 服务器的 IP 地址
      * 主 NTP 服务器的 IP 地址
      
      > [!NOTE]
      > 应用子网掩码和 DNS 设置时，可能需要等待几分钟。
    
   4. 可以选择配置 Web 代理服务器。
      
      > [!IMPORTANT]
      > 尽管 Web 代理服务器配置是可选项，仍应注意，在使用 Web 代理时，只能在此处配置它。 有关详细信息，请转到[配置设备的 Web 代理](../articles/storsimple/storsimple-configure-web-proxy.md)。
     
6. 按 Ctrl + C 可以退出安装向导。
8. 运行以下 cmdlet，以将设备指向 Microsoft Azure 政府门户（因为它默认指向公共 Azure 经典门户）。 这会重新启动两个控制器。 我们建议使用两个 PuTTY 会话以便同时连接两个控制器，这样可以查看每个控制器何时重新启动。
   
    `Set-CloudPlatform -AzureGovt_US`
   
   将显示确认消息。 接受默认值 (**Y**)。
9. 运行以下 cmdlet 以恢复安装：
   
    `Invoke-HcsSetupWizard`
   
    ![恢复安装向导](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. 接受网络设置。 接受每个设置后，会看到一条验证消息。
11. 出于安全原因，设备管理员密码会在第一个会话后过期，因此需要现在更改密码。 出现提示时，提供设备管理员密码。 有效的设备管理员密码必须介于 8 到 15 个字符之间。 密码必须包含以下各项的其中三个组合：小写字母、大写字母、数字和特殊字符。
    
    <br/>![StorSimple 注册设备 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. 安装向导中的最后一步向 StorSimple Device Manager 服务注册设备。 为此，将需要使用在[步骤 2：获取服务注册密钥](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key)中所获取的服务注册密钥。 提供注册密钥后，可能需要等待 2-3 分钟，然后设备才会被注册。
    
    > [!NOTE]
    > 可以随时按 Ctrl+C 退出安装向导。 如果已输入所有网络设置（DATA 0 的 IP 地址、子网掩码和网关），将保留这些条目。
    
    ![StorSimple 注册进度](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. 注册设备后，会显示一个服务数据加密密钥。 复制此密钥并将其保存到一个安全位置。 **向 StorSimple 设备管理器服务注册其他设备时，需要此密钥以及该服务注册密钥。** 有关此密钥的详细信息，请参阅 [StorSimple 安全性](../articles/storsimple/storsimple-8000-security.md) 。
    
    ![StorSimple 注册设备 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > 要复制串行控制台窗口中的文本，只需选择该文本。 然后应能够将其粘贴到剪贴板或任何文本编辑器中。
    > 
    > 请勿使用 Ctrl + C 复制服务数据加密密钥。 使用 Ctrl+C 会导致退出安装向导。 这样做的结果是，不会更改设备管理员密码，并且设备将还原为默认密码。
    
14. 退出串行控制台。
15. 返回到 Azure 政府门户，并完成以下步骤：
    
    1. 转到 StorSimple Device Manager 服务。
    2. 单击“设备”。 从设备列表中，确定要部署的设备。 通过查看状态验证该设备是否已成功连接到该服务。 设备状态应为“ **联机**”。
            
        如果设备状态为“ **脱机**”，请等待几分钟以便设备联机。
       
        如果几分钟后设备仍处于脱机状态，需要确保防火墙网络已按 [StorSimple 设备的网络要求](../articles/storsimple/storsimple-8000-system-requirements.md)中所述进行配置。
       
        确认端口 9354 已对出站通信打开，因为 StorSimple Device Manager 服务到设备之间通信的服务总线将使用此端口。

