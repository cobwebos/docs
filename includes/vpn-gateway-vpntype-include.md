* **PolicyBased:** PolicyBased Vpn 在经典部署模型中之前调用静态路由网关。 基于策略的 Vpn 加密，并直接通过基于配置与你的本地网络与 Azure 的 VNet 之间的地址前缀的组合的 IPsec 策略的 IPsec 隧道数据包。 策略 （或流量选择器） 通常定义为访问列表中的 VPN 设备配置。 为 PolicyBased VPN 类型的值是*PolicyBased*。 在使用基于策略的 VPN，请记住以下限制：
  
  * PolicyBased Vpn 可以**仅**基本网关 SKU 上使用。 此 VPN 类型不是与其他网关 Sku 兼容的。
  * 使用 PolicyBased VPN 时，你可以仅 1 隧道。
  * 你只能使用 PolicyBased Vpn S2S 连接，以及只为某些配置。 大多数 VPN 网关配置需要 RouteBased VPN。
* **RouteBased**: RouteBased Vpn 以前称为动态路由网关在经典部署模型中。 RouteBased Vpn IP 转发或表到直接数据包路由到其对应的隧道接口中使用"路由"。 隧道接口然后加密或解密入和移出隧道数据包。 策略 （或流量选择器） RouteBased vpn 配置为任意到任意 （或通配符）。 RouteBased VPN 类型的值是*RouteBased*。

