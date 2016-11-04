## 跨订阅对等
在此方案中，将创建属于不同订阅的两个 VNet 间的对等互连。

![跨订阅方案](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet 对等互连依赖基于角色的访问控制 (RBAC) 进行授权。对于跨订阅方案，需要先向将创建对等链接的用户授予充足权限：

> [!NOTE]
> 如果同一用户具有两个订阅的权限，可跳过下面的步骤 1-4。
> 
> 

<!---HONumber=AcomDC_0921_2016-->