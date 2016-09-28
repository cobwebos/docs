<!-- not suitable for Mooncake -->

## 如何在 Azure 门户预览中创建 VNet 对等互连

若要通过 Azure 门户预览基于上述方案创建 VNet 对等互连，请执行下面的步骤。

1. 从浏览器导航到 http://portal.azure.cn，如有必要，请使用 Azure 帐户登录。
2. 若要建立 VNET 对等互连，需要在两个 VNet 之间创建两个链接，每个方向各一个。可先创建 VNET1 到 VNET2 的 VNET 对等互连链接。在门户中，单击“浏览”> 选择“虚拟网络”

	![在 Azure 门户预览中创建 VNet 对等互连](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure01.png)

3. 在“虚拟网络”边栏选项卡中，选择“VNET1”，再依次单击“对等互连”和“添加”

	![选择对等互连](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure02.png)

4. 在“添加对等互连”边栏选项卡中，将对等互连链接命名为 LinkToVnet2，选择订阅和对等虚拟网络 VNET2，然后单击“确定”。

	![链接到 VNet](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure03.png)

5. 该 VNET 对等互连链接创建完成后。就可以看到链接状态如下所示：

	![链接状态](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure04.png)

6. 接下来创建 VNET2 到 VNET1 的 VNET 对等互连链接。在“虚拟网络”边栏选项卡中，选择“VNET2”，再依次单击“对等互连”和“添加”

	![从其他 VNet 对等](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure05.png)

7. 在“添加对等互连”边栏选项卡中，将对等互连链接命名为 LinkToVnet1，选择订阅和对等虚拟网络，然后单击“确定”。

	![创建虚拟网络磁贴](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure06.png)

8. 该 VNET 对等互连链接创建完成后。就可以看到链接状态如下所示：

	![最终链接状态](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure07.png)

9. 检查 LinkToVnet2 的状态，该状态现也变为“已连接”。

	![最终链接状态 2](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure08.png)

10. 请注意：仅在两个链接均连接的情况下建立 VNET 对等互连。

<!---HONumber=Mooncake_0919_2016-->