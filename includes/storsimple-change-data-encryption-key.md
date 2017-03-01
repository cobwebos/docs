<!--author=SharS last changed: 12/01/15-->

### <a name="step-1-authorize-a-device-to-change-the-service-data-encryption-key-in-the-azure-classic-portal"></a>步骤 1：授权某个设备来更改 Azure 经典门户中的服务数据加密密钥
通常情况下，设备管理员会请求服务管理员授权某个设备来更改服务数据加密密钥。 然后，服务管理员会授权该设备来更改密钥。

第一步是在 Azure 经典门户中执行的。 服务管理员可以从有资格接受授权的设备显示列表中选择某个设备。 然后，授权该设备来启动服务数据加密密钥更改过程。

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>可以授权哪些设备来更改服务数据加密密钥？
设备必须满足以下条件，才能接受授权以启动服务数据加密密钥更改：

* 设备必须联机才有资格接受服务数据加密密钥更改授权。
* 如果密钥更改尚未启动，可以在 30 分钟后再次授权同一台设备。
* 如果先前授权的设备尚未启动密钥更改，可以授权其他设备。 新设备获得授权之后，旧设备不能启动更改。
* 当正在进行服务数据加密密钥的滚动更新时，将无法授权设备。
* 可以在以下情况下授权设备：某些使用服务注册的设备已滚动更新，但其他设备却没有。 在这种情况下，符合条件的设备就是已完成服务数据加密密钥更改的设备。

> [!NOTE]
> 在 Azure 经典门户中，StorSimple 虚拟设备未显示在有权启动密钥更改的设备列表中。
> 
> 

执行以下步骤，选择并授权某个设备以启动服务数据加密密钥更改。

#### <a name="to-authorize-a-device-to-change-the-key"></a>授权某个设备以更改密钥
1. 在服务仪表板页上，单击“更改服务数据加密密钥”。
   
    ![更改服务加密密钥](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)
2. 在“更改服务数据加密密钥”对话框框中，选择并授权某个设备以启动服务数据加密密钥更改。 下拉列表中包含所有符合条件的授权设备。
3. 单击选中图标  ![选中图标](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png)。

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>步骤 2：使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改
在 Windows PowerShell for StorSimple 界面中对授权的 StorSimple 设备执行此步骤。

> [!NOTE]
> 除非已完成密钥的滚动更新，否则无法在 StorSimple Manager 服务的 Azure 经典门户中执行任何操作。
> 
> 

如果使用设备串行控制台连接到 Windows PowerShell 界面，请执行以下步骤。

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>启动服务数据加密密钥更改
1. 选择选项 1 以使用完全访问权限登录。
2. 在命令提示符处，键入：
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. cmdlet 成功完成后，你将收到新的服务数据加密密钥。 复制并保存此密钥，以供在此过程的步骤 3 中使用。 此密钥将用于更新已使用 StorSimple Manager 服务注册的所有剩余设备。
   
   > [!NOTE]
   > 此过程必须在授权 StorSimple 设备后的四小时内启动。
   > 
   > 
   
   然后，此新密钥将发送到该服务，以推送到所有已使用该服务注册的设备。 之后，服务仪表板上将显示警报。 该服务将禁用已注册设备上的所有操作，随后设备管理员将需要更新其他设备上的服务数据加密密钥。 但是，不会中断 I/O（将数据发送到云的主机）。
   
   如果已有一台设备注册到服务，滚动更新过程现已完成，你可以跳过下一步。 如果已有多台设备注册到服务，请继续执行步骤 3。

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>步骤 3：在其他 StorSimple 设备上更新服务数据加密密钥
如果已有多台设备注册到 StorSimple Manager 服务，则必须在 StorSimple 设备的 Windows PowerShell 界面中执行这些步骤。 必须使用在“步骤 2：使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改”中获取的密钥，才能更新已使用 StorSimple Manager 服务注册的所有剩余 StorSimple 设备。

执行以下步骤，更新设备上的服务数据加密。

#### <a name="to-update-the-service-data-encryption-key"></a>更新服务数据加密密钥
1. 使用 Windows PowerShell for StorSimple 连接到控制台。 选择选项 1 以使用完全访问权限登录。
2. 在命令提示符处，键入：
   
    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. 提供在[步骤 2：使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改](#to-initiate-the-service-data-encryption-key-change)中所获取的服务数据加密密钥。



<!--HONumber=Nov16_HO3-->


