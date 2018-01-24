## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>APIM 代理服务器在 TLS 握手中如何通过 SSL 证书进行响应

### <a name="clients-calling-with-sni-header"></a>调用时使用 SNI 标头的客户端
如果客户为代理配置了一个或多个自定义域，则 APIM 可以响应来自自定义域（例如 contoso.com）以及默认域（例如 apim-service-name.azure-api.net）的 HTTPS 请求。 APIM 根据服务器名称指示 (SNI) 标头中的信息使用合适的服务器证书进行响应。

### <a name="clients-calling-without-sni-header"></a>调用时不使用 SNI 标头的客户端
如果客户使用不发送 [SNI](https://tools.ietf.org/html/rfc6066#section-3) 标头的客户端，则 APIM 会根据以下逻辑创建响应：

* 如果服务仅为代理配置了一个自定义域，则默认证书是已颁发给代理自定义域的证书。
* 如果服务为代理配置了多个域（只有**高级**层才支持），则客户可以指定哪个证书应当作为默认证书。 若要设置默认证书，[defaultSslBinding](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/createorupdate#definitions_hostnameconfiguration) 属性应当设置为 true ("defaultSslBinding":"true")。 如果客户未设置该属性，则默认证书是颁发给 *.azure api.net 上托管的默认代理域的证书。

## <a name="support-for-putpost-request-with-large-payload"></a>对包含大型有效负载的 PUT/POST 请求的支持

当在 HTTPS 中使用客户端证书时，APIM 代理服务器支持包含大型有效负载的请求（例如，有效负载 > 40 KB）。 若要防止服务器的请求被冻结，客户可以在代理主机名上设置属性 ["negotiateClientCertificate": "true"](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/CreateOrUpdate#definitions_hostnameconfiguration)。 如果该属性设置为 true，则在进行 SSL/TLS 连接时将在交换任何 HTTP 请求之前请求证书。 由于该设置是在**代理主机名**级别应用的，因此，所有连接请求都会请求客户端证书。 客户可以为代理配置最多 20 个自定义域（只有**高级**层才支持）并避开此限制。

