<!--author=SharS last changed: 03/17/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>若要从 Azure 经典门户中安装更新 1.2
1. 在 Azure 经典门户中，转到**设备**页，选择你的设备。
2. 导航到**设备** > **配置**。
3. 下**网络接口**，应首先验证你有为 iSCSI 启用的至少一个网络接口。 然后找到已分配的网关的网络接口 （除了 DATA 0)。
4. 禁用具有分配网关的网络接口，并保存修改后的配置。 请注意网络接口设置将被保留，因此当重新启用了更高版本此网络接口，门户将恢复到原始设置。
5. 你现在可以[使用 Azure 经典门户来安装更新 1.2](#install-update-12-via-the-azure-classic-portal)。 按照从本过程的步骤 3 开始的说明。 安装所有更新后，你可以重新启用禁用的网络接口。

