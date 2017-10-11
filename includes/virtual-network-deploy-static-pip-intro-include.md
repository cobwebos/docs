可以在 Azure 中创建虚拟机 (Vm)，并将它们公开给公共 Internet，通过使用公共 IP 地址。 默认情况下，公共 Ip 是动态的和删除 VM 时，可能会更改与其关联的地址。 若要确保 VM 始终使用相同的公共 IP 地址，你需要创建静态公共 IP。 

你可以在 Vm 中实现静态公共 Ip 之前，有必要了解何时可以使用静态的公共 Ip，以及如何使用它们。 读取[IP 寻址概述](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md)若要了解有关在 Azure 中的 IP 寻址的详细信息。

