切换回浏览器窗口，其中包含 Azure 门户。

#### <a name="add-hostname"></a>添加主机名

单击“添加主机名”旁边的 **+** 图标。

![添加主机名](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

#### <a name="validate-hostname"></a>验证主机名

键入前面为其配置了 CNAME 记录的完全限定域名（例如 `www.contoso.com`），然后单击“验证”。

在“主机名记录类型”标头下选择“CNAME (www.example.com 或任何子域)”选项。

单击“添加主机名”将 DNS 名称添加到应用。

![将 DNS 名称添加到应用](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

新主机名可能需要经过一段时间后才会反映在应用的“自定义域”页面中。 请尝试刷新浏览器来更新数据。