### 步骤 1：导航到虚拟网络网关
<a id="step-1-navigate-to-the-virtual-network-gateway" class="xliff"></a>

1. 在 [Azure 门户](https://portal.azure.com)中导航到“所有资源”。 
2. 若要打开虚拟网关边栏选项卡，请导航到要删除的虚拟网关，然后单击它。

### 步骤 2：删除连接
<a id="step-2-delete-connections" class="xliff"></a>

1. 在“虚拟网络网关”边栏选项卡中，单击“连接”以查看连接到该网关的所有连接。
2. 在含有要删除的前缀的行上，单击 “...”，然后从下拉列表中选择“删除”。
3. 单击“是”以确认要删除该连接。 如有多个连接，请删除每个连接。

### 步骤 3：删除虚拟网络网关
<a id="step-3-delete-the-virtual-network-gateway" class="xliff"></a>

请注意，除了 S2S 配置，如果还有此 VNet 的 P2S 配置，则删除虚拟网关将自动断开所有 P2S 客户端且不发出警告。

1. 在虚拟网关边栏选项卡中，单击“概览”。
2. 在“概述”边栏选项卡上，单击“删除”以删除网关。
