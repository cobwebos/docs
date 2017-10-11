<!--author=SharS last changed: 12/01/15-->

### <a name="step-1-authorize-a-device-to-change-the-service-data-encryption-key-in-the-azure-classic-portal"></a>步骤 1： 授权某个设备更改服务数据加密密钥在 Azure 经典门户
通常情况下，设备管理员会请求服务管理员授权某个设备更改服务数据加密密钥。 服务管理员然后就会授权该设备更改密钥。

Azure 经典门户中执行此步骤。 服务管理员可以从显示的资格接受授权的设备列表中选择一个设备。 然后会授权该设备，以启动服务数据加密密钥更改过程。

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>哪些设备有权更改服务数据加密密钥？
它可以接受授权以启动服务数据加密密钥更改之前，设备必须满足以下条件：

* 设备必须联机才有资格接受服务数据加密密钥更改授权。
* 如果尚未启动密钥更改，你可以在 30 分钟后再次授权同一设备。
* 你可以授权其他设备，前提是此前获得授权的设备尚未启动密钥更改。 新设备获得授权后，旧设备不能启动更改。
* 正在进行的服务数据加密密钥滚动更新时，无法授权设备。
* 一些与服务已注册的设备具有滚动加密，而其他设备有不时，可以授权某个设备。 在这种情况下，符合条件的设备是那些已完成服务数据加密密钥更改。

> [!NOTE]
> 在 Azure 经典门户中，有权启动密钥更改的设备的列表中未显示 StorSimple 虚拟设备。
> 
> 

执行以下步骤以选择并授权某个设备启动服务数据加密密钥更改。

#### <a name="to-authorize-a-device-to-change-the-key"></a>若要授权某个设备更改密钥
1. 在服务仪表板页上，单击**更改服务数据加密密钥**。
   
    ![更改服务加密密钥](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)
2. 在**更改服务数据加密密钥**对话框框中，选择并授权某个设备启动服务数据加密密钥更改。 下拉列表中有可进行授权的所有合格设备。
3. 单击复选图标 ![选中图标](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png)。

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>步骤 2： 使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改
在 Windows PowerShell for StorSimple 接口在授权的 StorSimple 设备上执行此步骤。

> [!NOTE]
> 可以在你的 StorSimple Manager 服务的 Azure 经典门户不执行任何操作，直到完成密钥滚动更新。
> 
> 

如果将设备串行控制台连接到的 Windows PowerShell 界面，执行以下步骤。

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>若要启动服务数据加密密钥更改
1. 选择选项 1 登录具有完全访问权限。
2. 在命令提示符处，键入：
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. 该 cmdlet 成功完成后，你将收到一个新的服务数据加密密钥。 复制并保存此密钥，以便使用此过程的步骤 3 中。 此密钥将用于更新已注册的 StorSimple Manager 服务的所有剩余设备。
   
   > [!NOTE]
   > 此过程必须在授权 StorSimple 设备的四个小时内启动。
   > 
   > 
   
   此新密钥会然后发送到服务推送到注册服务的所有设备。 然后，警报将显示在服务仪表板。 该服务会禁用已注册的设备上的所有操作和设备管理员然后将需要更新其他设备上的服务数据加密密钥。 但是，不会中断 I/o （主机向云发送数据）。
   
   如果你有单个设备注册到服务，此时滚动更新过程已完成，并且你可以跳过下一步。 如果你有注册到服务的多个设备，则继续执行步骤 3。

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>步骤 3： 更新服务数据加密密钥在其他 StorSimple 设备上
如果你有注册到 StorSimple Manager 服务的多个设备，必须在 StorSimple 设备的 Windows PowerShell 界面中执行这些步骤。 在步骤 2 中获得的密钥： 使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改必须用于更新的 StorSimple Manager 服务中注册所有剩余 StorSimple 设备。

执行以下步骤以更新你的设备上的服务数据加密。

#### <a name="to-update-the-service-data-encryption-key"></a>若要更新服务数据加密密钥
1. 使用 Windows PowerShell for StorSimple 连接到控制台。 选择选项 1 登录具有完全访问权限。
2. 在命令提示符处，键入：
   
    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. 提供你在中获得的服务数据加密密钥[步骤 2： 使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改](#to-initiate-the-service-data-encryption-key-change)。

