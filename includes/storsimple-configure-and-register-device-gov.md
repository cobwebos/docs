<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>配置和注册设备
1. 访问 StorSimple 设备串行控制台上的 Windows PowerShell 接口。 有关说明，请参阅 [使用 PuTTY 连接到设备串行控制台](#use-putty-to-connect-to-the-device-serial-console) 。 **请务必严格遵照该步骤，否则将无法访问控制台。**
2. 在打开的会话中，按一次 Enter 以启动命令提示符。 
3. 系统将提示你选择要为设备设置的语言。 指定语言后，按 Enter。 
   
    ![StorSimple 配置和注册设备 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. 在显示的串行控制台菜单中，选择选项 1 以具有完全访问权限进行登录。 
   
    ![StorSimple 注册设备 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
5. 执行以下步骤，为设备配置最低要求的网络设置。
   
   > [!IMPORTANT]
   > 需要在该设备的活动控制器上执行这些配置步骤。  串行控制台菜单指示标题消息中的控制器状态。 如果未连接到活动控制器，请断开连接，然后连接到活动控制器。
   > 
   > 
   
   1. 在命令提示符处，键入你的密码。 默认设备密码为 **Password1**。
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
      > 
      > 
   4. 可以选择配置 Web 代理服务器。
      
      > [!IMPORTANT]
      > 尽管 Web 代理服务器配置是可选项，仍应注意，在使用 Web 代理时，只能在此处配置它。 有关详细信息，请转到 [配置你的设备的 Web 代理](../articles/storsimple/storsimple-configure-web-proxy.md)。 
      > 
      > 
6. 按 Ctrl + C 可以退出安装向导。
7. 按以下步骤安装更新：
   
   1. 使用以下 cmdlet 在两个控制器上设置 IP：
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. 在命令提示符处，运行 `Get-HcsUpdateAvailability`。 当更新可用时，应通知用户。
   3. 运行 `Start-HcsUpdate`。 可以在任何节点上运行此命令。 更新将应用第一个控制器上，该控制器将故障转移，随后更新在另一个控制器上应用。
      
      可以通过运行 `Get-HcsUpdateStatus` 监视更新进度。    
      
      以下示例输出显示更新正在进行。
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   : 
      ````
      
      以下示例输出指示更新已完成。
      
      ````
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      
      It may take up to 11 hours to apply all the updates, including the Windows Updates.
8. After all the updates are successfully installed, run the following cmdlet to confirm that the software updates were applied correctly:
   
     `Get-HcsSystem`
   
    You should see the following versions:
   
   * HcsSoftwareVersion: 6.3.9600.17491
   * CisAgentVersion: 1.0.9037.0
   * MdsAgentVersion: 26.0.4696.1433
9. Run the following cmdlet to confirm that the firmware update was applied correctly:
   
    `Start-HcsFirmwareCheck`.
   
     The firmware status should be **UpToDate**.
10. Run the following cmdlet to point the device to the Microsoft Azure Government portal (because it points to the public Azure classic portal by default). This will restart both controllers. We recommend that you use two PuTTY sessions to simultaneously connect to both controllers so that you can see when each controller is restarted.
    
     `Set-CloudPlatform -AzureGovt_US`
    
    You will see a confirmation message. Accept the default (**Y**).
11. Run the following cmdlet to resume setup:
    
     `Invoke-HcsSetupWizard`
    
     ![Resume setup wizard](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    When you resume setup, the wizard will be the Update 1 version (which corresponds to version 17469). 
12. Accept the network settings. You will see a validation message after you accept each setting.
13. For security reasons, the device administrator password expires after the first session, and you will need to change it now. When prompted, provide a device administrator password. A valid device administrator password must be between 8 and 15 characters. The password must contain three of the following: lowercase, uppercase, numeric, and special characters.
    
    <br/>![StorSimple register device 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. The final step in the setup wizard registers your device with the StorSimple Manager service. For this, you will need the service registration key that you obtained in [Step 2: Get the service registration key](#step-2-get-the-service-registration-key). After you supply the registration key, you may need to wait for 2-3 minutes before the device is registered.
    
    > [!NOTE]
    > You can press Ctrl + C at any time to exit the setup wizard. If you have entered all the network settings (IP address for Data 0, Subnet mask, and Gateway), your entries will be retained.
    > 
    > 
    
    ![StorSimple registration progress](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. After the device is registered, a Service Data Encryption key will appear. Copy this key and save it in a safe location. **This key will be required with the service registration key to register additional devices with the StorSimple Manager service.** Refer to [StorSimple security](../articles/storsimple/storsimple-security.md) for more information about this key.
    
    ![StorSimple register device 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > To copy the text from the serial console window, simply select the text. You should then be able to paste it in the clipboard or any text editor. 
    > 
    > DO NOT use Ctrl + C to copy the service data encryption key. Using Ctrl + C will cause you to exit the setup wizard. As a result, the device administrator password will not be changed and the device will revert to the default password.
    > 
    > 
16. Exit the serial console.
17. Return to the Azure Government Portal, and complete the following steps:
    
    1. Double-click your StorSimple Manager service to access the **Quick Start** page.
    2. Click **View connected devices**.
    3. On the **Devices** page, verify that the device has successfully connected to the service by looking up the status. The device status should be **Online**.
       
        ![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        If the device status is **Offline**, wait for a couple of minutes for the device to come online. 
       
        If the device is still offline after a few minutes, then you need to make sure that your firewall network was configured as described in [networking requirements for your StorSimple device](../articles/storsimple/storsimple-system-requirements.md). 
       
        Verify that port 9354 is open for outbound communication as this is used by the service bus for StorSimple Manager service-to-device communication.



<!--HONumber=Nov16_HO3-->


