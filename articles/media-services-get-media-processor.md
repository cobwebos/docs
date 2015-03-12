<properties linkid="develop-media-services-how-to-guides-create-media-processor" urlDisplayName="Create a Media Processor" pageTitle="如何创建媒体处理器 - Azure" metaKeywords="" description="了解如何创建一个媒体处理器组件用来为 Azure Media Services 编码、转换格式、加密或解密媒体内容。代码示例用 C# 编写且使用 Media Services SDK for .NET。" metaCanonical="" services="media-services" documentationCenter="" title="How to: Get a Media Processor Instance" authors="migree" solutions="" manager="" editor="" />





<h1>如何：获取媒体处理器实例</h1>
本文是介绍 Azure Media Services 编程的系列主题中的一篇。前一个主题是[如何：创建加密的资产并上载到存储中](/zh-cn/documentation/articles/media-services-create-encrypted-asset-upload-storage/)。

在 Media Services 中，媒体处理器是完成特定处理任务（例如，对媒体内容进行编码、格式转换、加密或解密）的组件。通常，当你创建一个任务以便对媒体内容进行编码、加密或格式转换时，就需要创建一个媒体处理器。

下表提供了每个可用媒体处理器的名称和说明。

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>媒体处理器名称</th>
       <th>说明</th>
	<th>更多信息</th>
    </tr>
  </thead>
  <tbody>
    <tr>
       <td>Azure Media Encoder</td>
       <td>让你使用 Media Encoder 运行编码任务。</td>
       <td><a href="http://msdn.microsoft.com/library/jj129582.aspx">Azure Media Encoder 的任务预设字符串</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Packager</td>
        <td>让你将媒体资产从 .mp4 格式转换为平滑流式处理格式。还可让你将媒体资产从平滑流式处理格式转换为 Apple HTTP 实时流 (HLS) 格式。</td>
		<td><a href="http://msdn.microsoft.com/library/hh973635.aspx">Azure Media Packager 的任务预设字符串</a></td>
    </tr>
    <tr>
        <td>Microsoft Azure Media Encryptor</td>
        <td>让你使用 PlayReady 保护加密媒体资产。</td>
        <td><a href="http://msdn.microsoft.com/library/hh973610.aspx">Azure Media Packager 的任务预设字符串</a></td>
    </tr>
    <tr>
        <td>Azure Media Indexer</td>
        <td>使媒体文件和内容可搜索，以及生成隐藏字幕跟踪和关键字。</td>
		<td>不适用</td>
    </tr>
    <tr>
        <td>存储解密</td>
        <td>让你解密使用存储加密技术加密的媒体资产。</td>
		<td>不适用</td>
    </tr>
  </tbody>
</table>

<br />

以下方法演示了如何获取媒体处理器实例。该代码示例假设使用名为 **_context** 的模块级变量来引用[如何：以编程方式连接到 Media Services] 部分中所述的服务器上下文。

<pre><code>
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
     var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
</code></pre>

<h2>后续步骤</h2>
了解如何获取媒体处理器实例后，请转到[如何为资产编码][]主题，其中说明了如何使用 Azure Media Encoder 对资产进行编码。

[如何为资产编码]: /zh-cn/documentation/articles/media-services-encode-asset/
[Azure Media Encoder 的任务预设字符串]: http://msdn.microsoft.com/library/jj129582.aspx
[如何：以编程方式连接到 Media Services]: /zh-cn/documentation/articles/media-services-set-up-computer/
