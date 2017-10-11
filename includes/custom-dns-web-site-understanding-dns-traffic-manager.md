域名系统 (DNS) 用于在 internet 上查找操作。 例如，当你在浏览器中，输入地址，或单击 web 页上的链接，它使用 DNS 将域转换为 IP 地址。 IP 地址是有点像街道地址，但它不是很好的友好。 例如，它是可以更轻松地记住 DNS 名称，例如**contoso.com**比记住 192.168.1.88 或 2001:0:4137:1f67:24a2:3888:9cce:fea3 的 IP 地址。

DNS 系统基于*记录*。 记录关联特定*名称*，如**contoso.com**、 使用 IP 地址或其他的 DNS 名称。 当某个应用程序，如 web 浏览器中，查找在 DNS 中的名称时，它将找到记录，并使用它所指向的地址。 如果它指向的值是 IP 地址，则浏览器将使用该值。 如果它指向另一个 DNS 名称，然后应用程序必须再次执行解析。 从根本上讲，所有名称解析将都结束 IP 地址。

在创建 Azure 网站时，DNS 名称是自动分配给站点。 此名称采用以下格式的 **&lt;yoursitename&gt;。 azurewebsites.net**。 在你的网站添加为 Azure 流量管理器终结点时，你的网站是可通过访问 **&lt;yourtrafficmanagerprofile&gt;。 trafficmanager.net**域。

> [!NOTE]
> 当你的网站配置为 Traffic Manager 终结点时，你将使用**。 trafficmanager.net**地址创建 DNS 记录时。
> 
> 你只能使用 CNAME 记录使用流量管理器
> 
> 

此外还有多种类型的记录，每个都有其自己的功能和限制，但是对于到配置为 Traffic Manager 终结点的网站，我们只关心有关某个;*CNAME*记录。

### <a name="cname-or-alias-record"></a>CNAME 或别名记录
CNAME 记录将映射*特定*DNS 名称，如**mail.contoso.com**或**www.contoso.com**，到另一个 （规范） 域名。 对于使用流量管理器的 Azure 网站，规范域名是 **&lt;myapp >。 trafficmanager.net** Traffic Manager 配置文件的域名。 CNAME 创建后，创建一个别名 **&lt;myapp >。 trafficmanager.net**域名。 CNAME 条目将解析为 IP 地址的你 **&lt;myapp >。 trafficmanager.net**域名自动，因此如果该网站的 IP 地址发生更改，无需采取任何措施。

一旦流量到达 Traffic Manager，它然后将流量路由到你的网站，使用的负载平衡的方法为配置它。 这是对你的网站的访问者完全透明。 他们将只能看到其浏览器中的自定义域名。

> [!NOTE]
> 某些域注册机构只允许您使用一个 CNAME 记录，如时映射子域**www.contoso.com**，和非根名称，如**contoso.com**。 有关 CNAME 记录的详细信息，请参阅由您的注册机构提供的文档<a href="http://en.wikipedia.org/wiki/CNAME_record">CNAME 记录上的 Wikipedia 条目</a>，或<a href="http://tools.ietf.org/html/rfc1035">IETF 域名-实现和规范</a>文档。
> 
> 

