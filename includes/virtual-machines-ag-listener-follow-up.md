创建可用性组侦听器后，可能需要调整侦听器资源的 RegisterAllProvidersIP 和 HostRecordTTL 群集参数。 这些参数可以减少故障转移，这可能会防止连接超时后的重新连接时间。 有关这些参数，以及示例代码的详细信息，请参阅[创建或配置可用性组侦听器](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)。

