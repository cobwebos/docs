<!--author=SharS last changed: 02/22/2016-->

### <a name="to-configure-and-register-the-device"></a>若要配置和注册设备
1. 访问在你的 StorSimple 设备串行控制台上的 Windows PowerShell 接口。 请参阅[使用 PuTTY 连接到设备串行控制台](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console)有关的说明。 **请务必严格遵照该过程或你将无法访问控制台。**
2. 在打开的会话，按 enter 键一次以启动命令提示符。
3. 系统将提示你选择的语言，你想要为你设备设置。 指定的语言，然后按 enter 键。
   
    ![StorSimple 配置和注册的设备 1](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. 在串行控制台菜单中显示，选择选项 1 以具有完全访问权限登录。
   
    ![StorSimple 注册设备 2](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. 执行以下步骤以配置你的设备的最小所需的网络设置。
   
   > [!IMPORTANT]
   > 这些配置步骤需要设备在活动控制器上执行。 串行控制台菜单中指示横幅消息中的控制器状态。 如果未连接到主动控制器，断开连接，然后连接到主动控制器。
   > 
   > 
   
   1. 在命令提示符处，键入你的密码。 默认设备密码**Password1**。
   2. 键入下列命令：
      
        `Invoke-HcsSetupWizard`
   3. 安装向导将显示帮助你配置设备的网络设置。 提供以下信息：
      
      * DATA 0 网络接口的 IP 地址
      * 子网掩码
      * Gateway
      * 主 DNS 服务器的 IP 地址
      * 主 NTP 服务器的 IP 地址
      
      > [!NOTE]
      > 你可能需要等待几分钟时间的子网掩码和要应用 DNS 设置。
      > 
      > 
   4. 或者，配置你的 web 代理服务器。
      
      > [!IMPORTANT]
      > 尽管 web 代理配置是可选的但请注意，如果你使用 web 代理，则可以仅此处配置它。 有关详细信息，请转到[配置你的设备的 web 代理](../articles/storsimple/storsimple-configure-web-proxy.md)。
      > 
      > 
6. 按 Ctrl + C 退出安装向导。
7. 安装更新，如下所示：
   
   1. 使用以下 cmdlet 在两个控制器上设置 Ip:
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. 在命令提示符处，运行`Get-HcsUpdateAvailability`。 应将通知有可用更新。
   3. 运行 `Start-HcsUpdate`。 你可以在任何节点上运行此命令。 将在第一个控制器上应用更新，控制器将故障转移，然后在另一个控制器上应用这些更新。
      
      你可以通过运行监视更新进度`Get-HcsUpdateStatus`。    
      
      下面的示例输出显示正在进行的更新。
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      ````
      
      下面的示例输出指示更新完成。
      
      ```
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      ```
      
      它可能需要长达 11 小时，将所有更新，包括 Windows 更新应用。
8. 运行以下 cmdlet 以指向 Microsoft Azure 政府版门户设备 （因为它指向公共 Azure 经典门户默认情况下）。 这将重新启动两个控制器。 我们建议使用两个 PuTTY 会话，以便你可以看到每个控制器重新启动时同时连接到两个控制器。
   
    `Set-CloudPlatform -AzureGovt_US`
   
   你将看到一条确认消息。 接受默认值 (**Y**)。
9. 运行以下 cmdlet 以继续运行安装程序：
   
    `Invoke-HcsSetupWizard`
   
    ![恢复安装程序向导](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
   当你继续安装程序时，该向导将更新 2 版本。
10. 接受的网络设置。 接受每个设置后，你将看到一条验证消息。
11. 出于安全原因，设备管理员密码过期后的第一个会话，并将需要立即更改。 出现提示时，提供设备管理员密码。 有效的设备管理员密码必须介于 8 到 15 个字符之间。 密码必须包含的以下三种： 小写、 大写、 数字和特殊字符。
    
    <br/>![StorSimple 注册设备 5](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. 安装向导中的最后一步使用 StorSimple Manager 服务注册你的设备。 为此，你将需要在中获取服务注册密钥[步骤 2： 获取服务注册密钥](../articles/storsimple/storsimple-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key)。 提供注册密钥后，你可能需要等待 2-3 分钟，然后注册该设备。
    
    > [!NOTE]
    > 可以随时可以退出安装向导中按 Ctrl + C。 如果您输入的所有网络设置 （Data 0、 子网掩码和网关的 IP 地址），将保留你的条目。
    > 
    > 
    
    ![StorSimple 注册进度](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. 注册设备之后，将显示一个服务数据加密密钥。 复制此密钥并将其保存在安全的位置。 **使用服务注册密钥向 StorSimple Manager 服务注册其他设备将需要使用此密钥。** 请参阅[StorSimple 安全性](../articles/storsimple/storsimple-security.md)有关此密钥的详细信息。
    
    ![StorSimple 注册设备 7](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > 若要从串行控制台窗口复制文本，只需选择的文本。 你应能够将其粘贴到剪贴板或任何文本编辑器中。
    > 
    > 请勿使用 Ctrl + C 复制服务数据加密密钥。 使用 Ctrl + C 将导致你退出安装向导。 因此，将不会更改设备管理员密码，设备将恢复到默认密码。
    > 
    > 
14. 退出串行控制台。
15. 返回到 Azure Government 门户中，并完成以下步骤：
    
    1. 双击你的 StorSimple Manager 服务访问**快速启动**页。
    2. 单击**查看连接的设备**。
    3. 上**设备**页上，验证设备是否成功连接到该服务通过查看状态。 设备状态应该是**联机**。
       
        ![StorSimple 设备页](./media/storsimple-configure-and-register-device-gov-u2/HCS_DeviceOnline-gov-include.png)
       
        如果设备状态为**脱机**，请稍等几分钟以便设备联机。
       
        如果设备是仍处于脱机状态后几分钟，则需要确保你的防火墙网络已配置中所述[StorSimple 设备的网络要求](../articles/storsimple/storsimple-system-requirements.md)。
       
        验证端口 9354 打开用于出站通信，因为这用于 service bus StorSimple Manager 服务到设备通信。

