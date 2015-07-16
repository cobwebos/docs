<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">资源</th>
   <th align="left" valign="middle">默认限制</th>
   <th align="left" valign="middle">最大限制</th>
</tr>
<tr>
   <td valign="middle"><p>每个订阅的<a href="http://msdn.microsoft.com/zh-cnlibrary/azure/jj156007.aspx">虚拟网络数</a><sup>1</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>每个虚拟网络的总虚拟机数<sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>虚拟机或角色实例的并发 TCP 连接</p></td>
   <td valign="middle"><p>500K</p></td>
   <td valign="middle"><p>500K</p></td>
</tr>
<tr>
   <td valign="middle"><p>每个终结点的访问控制列表 (ACL) 数<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>每个虚拟网络的本地网络站点</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>每个虚拟网络支持单个[虚拟网络网关](http://msdn.microsoft.com/zh-cn/library/azure/jj156210.aspx)。

<sup>2</sup>虚拟机总数包括虚拟机和 Web/辅助角色实例。

<sup>3</sup>虚拟机的输入终结点支持 ACL。对于 web/辅助角色，在输入和实例输入终结点上支持它。

<!---HONumber=HO63-->