# [概述](media-services-overview.md)
## [概念](media-services-concepts.md)
## [定价](https://azure.microsoft.com/pricing/details/media-services/)
## [发行说明](media-services-release-notes.md)
# 入门
## [创建和管理帐户](media-services-portal-create-account.md)
## [设置开发环境](media-services-set-up-computer.md)
## 点播视频
### [门户](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## 实时传送视频流
### [门户](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# 如何
## 管理
### [在门户中管理流式处理终结点](media-services-portal-manage-streaming-endpoints.md)
### 管理实体
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [使用 PowerShell 管理帐户](media-services-manage-with-powershell.md)
### [使用 Media Encoder Standard 剪辑视频](media-services-crop-video.md)
### [如何：轮转存储访问密钥后更新媒体服务](media-services-roll-storage-access-keys.md)
### [配额和限制](media-services-quotas-and-limitations.md)
### 筛选器
#### [使用 Azure 媒体服务 .NET SDK 创建筛选器](media-services-dotnet-dynamic-manifest.md)
#### [如何使用 Media Encoder Standard 对资产进行编码](media-services-rest-encode-asset.md)
### 以编程方式连接
#### [.NET](media-services-dotnet-connect-programmatically.md)
#### [REST](media-services-rest-connect-programmatically.md)

## 上载内容
### 将文件上载到帐户中
#### [门户](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [复制现有的 Blob](media-services-copying-existing-blob.md)

## 编码
### [内容](media-services-encode-asset.md)
#### 使用 Media Encoder Standard 为资产编码
##### [门户](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
#### [如何使用 Media Encoder Standard 通过 .NET 来生成缩略图](media-services-dotnet-generate-thumbnail-with-mes.md)
#### [高级编码](media-services-advanced-encoding-with-mes.md)
##### [媒体编码器高级工作流](media-services-encode-with-premium-workflow.md)
##### [媒体编码器高级工作流教程](media-services-media-encoder-premium-workflow-tutorials.md)
##### [使用工作流设计器创建高级编码工作流](media-services-workflow-designer.md)
##### [使用多个输入的高级工作流](media-services-media-encoder-premium-workflow-multiplefilesinput.md)

#### 架构 
#####[Media Encoder Standard](media-services-mes-schema.md)
#####[输入元数据](media-services-input-metadata-schema.md)
#####[输出元数据](media-services-output-metadata-schema.md)

#### 旧编码器
##### [使用 Azure Media Packager](media-services-static-packaging.md)

### [实时流](media-services-manage-channels-overview.md)
#### [本地编码器](media-services-live-streaming-with-onprem-encoders.md)
#### 本地编码器教程
##### [门户](media-services-portal-live-passthrough-get-started.md)
##### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [使用云编码器实时传送视频流](media-services-manage-live-encoder-enabled-channels.md)
#### 云编码器教程
##### [门户](media-services-portal-creating-live-encoder-enabled-channel.md)
##### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
#### [配置用于云编码器的本地编码器](media-services-live-encoders-overview.md)
#### [处理长时间运行的操作](media-services-dotnet-long-operations.md)
#### [碎片化的 MP4 实时引入规范](media-services-fmp4-live-ingest-overview.md)
#### [动态打包](media-services-dynamic-packaging-overview.md)

### 媒体处理
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)

### 为单比特率实时流配置编码器
#### [Elemental Live 编码器](media-services-configure-elemental-live-encoder.md)
#### [FMLE 编码器 ](media-services-configure-fmle-live-encoder.md)
#### [NewTek TriCaster 编码器](media-services-configure-tricaster-live-encoder.md)
#### [Wirecast 编码器](media-services-configure-wirecast-live-encoder.md)

## [保护](media-services-content-protection-overview.md)
### [在门户中配置内容保护](media-services-portal-protect-content.md)
### [为流配置 AES-128 明文密钥](media-services-protect-with-aes128.md)
### [使用 AMS REST API 通过存储空间加密来加密内容](media-services-rest-storage-encryption.md)
### [媒体服务 PlayReady 许可证模板概述](media-services-playready-license-template-overview.md)
### [DRM 许可证传送](media-services-deliver-keys-and-licenses.md)
### [使用合作伙伴将 Widevine 许可证传送到 Azure 媒体服务](media-services-licenses-partner-integration.md)
### [使用 PlayReady 和/或 Widevine 动态通用加密](media-services-protect-with-drm.md)
### [使用 Azure 媒体服务流式传输受 Apple FairPlay 保护的 HLS 内容](media-services-protect-hls-with-fairplay.md)
### [使用多重 DRM 的 CENC 和访问控制：Azure 与 Azure 媒体服务的参考设计和实现](media-services-cenc-with-multidrm-access-control.md)

### 资产传送
#### 配置资产传送策略
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST](media-services-rest-configure-asset-delivery-policy.md)
### 创建内容密钥
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### 配置内容密钥授权策略
#### [门户](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)

## [分析](media-services-analytics-overview.md)
### [使用 Indexer 2 进行处理](media-services-process-content-with-indexer2.md)
### [使用 Indexer 进行处理](media-services-index-content.md)
### [使用 Hyperlapse 进行处理](media-services-hyperlapse-content.md)
### [使用 Face Detector 进行处理](media-services-face-and-emotion-detection.md)
### [使用 Motion Detector 进行处理](media-services-motion-detection.md)
### [使用 Face Redaction 进行处理](media-services-face-redaction.md)
### [使用视频缩略图进行处理](media-services-video-summarization.md)
### [使用 OCR 进行处理](media-services-video-optical-character-recognition.md)

## 缩放
### [媒体处理](media-services-scale-media-processing-overview.md)
#### [门户](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
### 流式处理终结点
#### [门户](media-services-portal-scale-streaming-endpoints.md)

## [传送内容](media-services-deliver-content-overview.md)
### [筛选器和动态清单概述](media-services-dynamic-manifest-overview.md)
### 创建筛选器
#### [.NET](media-services-dotnet-dynamic-manifest.md)
#### [REST](media-services-rest-dynamic-manifest.md)
### 发布内容
#### [门户](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [通过下载传送](media-services-deliver-asset-download.md)
### [故障转移流式处理方案](media-services-implement-failover.md)

## 使用
### [使用现有播放器播放媒体](media-services-playback-content-with-existing-players.md)
### [使用媒体播放器播放媒体](media-services-develop-video-players.md)
### 其他播放选项
#### [平滑流式处理 Windows 应用商店应用程序](media-services-build-smooth-streaming-apps.md)
#### [使用 DASH.js 的 HTML5 应用程序](media-services-embed-mpeg-dash-in-html5.md)
#### [Adobe Open Source Media Framework 播放器](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [客户端插入广告](media-services-inserting-ads-on-client-side.md)

## 集成
### [媒体服务扩展中的 CDN 缓存策略](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### [授权 Microsoft†" 平滑流式处理客户端移植工具包](media-services-sspk.md)
### [跨多个存储帐户管理资产](meda-services-managing-multiple-storage-accounts.md)
### [使用 Axinom 将 Widevine 许可证传送到 Azure 媒体服务](media-services-axinom-integration.md)
### [使用 castLabs 将 Widevine 许可证传送到 Azure 媒体服务](media-services-castlabs-integration.md)
### [Widevine 许可证模板概述](media-services-widevine-license-template-overview.md)

## 监视
### 检查作业进度
#### [REST](media-services-rest-check-job-progress.md)
#### [门户](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [使用队列存储监视作业通知](media-services-dotnet-check-job-progress-with-queues.md)

## 故障排除
### [常见问题](media-services-frequently-asked-questions.md)
### [实时流式处理故障排除指南](media-services-troubleshooting-live-streaming.md)
###[错误代码](media-services-error-codes.md)
###[重试逻辑](media-services-retry-logic-in-dotnet-sdk.md)

# 引用
## [媒体服务 .NET SDK](media-services-dotnet-how-to-use.md)
## [媒体服务 REST API](media-services-rest-how-to-use.md)
## [媒体编码器高级工作流格式和编解码器](media-services-premium-workflow-encoder-formats.md)
## [Media Encoder Standard 格式和编解码器](media-services-media-encoder-standard-formats.md)

# 相关内容
## [Azure 媒体服务社区](media-services-community.md)











<!--HONumber=Nov16_HO2-->


