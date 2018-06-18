| 资源 | 默认限制 |
| --- | :--- |
| 每个群集的最大节点数 | 100 |
| 每个节点的最大 Pod 数（[带 Kubenet 的基本网络][basic-networking]） | 110 |
| 每个节点的最大 Pod 数（[带 Azure CNI 的高级网络][advanced-networking]） | 30 |
| 每个订阅的最大群集数 | 20<sup>1</sup> |

<sup>1</sup> 创建 [Azure 支持请求][azure-support]可请求提高上限。<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest