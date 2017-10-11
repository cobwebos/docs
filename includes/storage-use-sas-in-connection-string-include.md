如果你拥有授予对存储帐户中的资源访问的共享的访问签名 (SAS) URL，你可以在连接字符串中使用 SAS。 由于该 SAS 包含对请求进行身份验证所需的信息，使用 SAS 连接字符串提供协议、 服务终结点和所需的凭据来访问资源。

若要创建的连接字符串，包含共享的访问签名，请采用以下格式指定字符串：

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

尽管该连接字符串必须包含至少一个，每个服务终结点是可选的。

> [!NOTE]
> 作为最佳做法建议你使用 SAS 使用 HTTPS。
>
> 如果您在配置文件中的连接字符串中指定 SAS，你可能需要在 URL 中的特殊字符进行编码。
>
>

### <a name="service-sas-example"></a>服务 SAS 示例
下面是一个包含服务 SAS 为 Blob 存储的连接字符串的示例：

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

而以下是使用编码的特殊字符的相同的连接字符串示例：

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>帐户 SAS 示例
下面是包括帐户 SAS 用于 Blob 和文件的存储连接字符串的示例。 请注意，未指定这两个服务的终结点：

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

而以下是相同的连接字符串，使用 URL 编码的示例：

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

