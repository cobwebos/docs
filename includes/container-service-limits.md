| 资源 | 默认限制 |
| --- | :--- |
| 每个群集的最大节点数 | 100 |
| 每个节点的最大 Pod 数（[带 Kubenet 的基本网络][basic-networking]） | 110 |
| 每个节点的最大 Pod 数（[带 Azure CNI 的高级网络][advanced-networking]） | 30<sup>1</sup> |
| 每个订阅的最大群集数 | 20<sup>2</sup> |

<sup>1</sup> 可以通过 ARM 模板部署自定义此值。 请参阅[此处的][arm-deployment-example]示例。<br />
<sup>2</sup> 可以创建 [Azure 支持请求][azure-support]来请求提高上限。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
