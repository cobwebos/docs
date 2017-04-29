<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>通过 Azure 经典门户安装 Update 1.2
1. 在 Azure 经典门户中转到“设备”页，并选择你的设备。
2. 导航到“设备” > “配置”。
3. 在“网络接口”下，请先确认至少有一个已启用 iSCSI 的网络接口。 然后，找到分配了网关的网络接口（DATA 0 除外）。
4. 禁用分配了网关的网络接口，然后保存修改后的配置。 请注意，系统将保留网络接口设置，因此，稍后重新启用此网络接口时，门户将还原为原始设置。
5. 现在可以[使用 Azure 经典门户安装 Update 1.2](#install-update-12-via-the-azure-classic-portal)。 请遵循本过程步骤 3 开始的说明。 安装所有更新后，可以重新启用已禁用的网络接口。

