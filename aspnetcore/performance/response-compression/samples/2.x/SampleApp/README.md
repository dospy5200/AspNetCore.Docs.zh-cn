# <a name="response-compression-sample-application-aspnet-core-2x"></a>ASP.NET Core 2.x (的应用程序的响应压缩示例) 

此示例演示如何使用 ASP.NET Core 2.x 响应压缩中间件来压缩 HTTP 响应。 该示例演示了用于文本和图像响应的 Gzip、Brotli 和自定义压缩提供程序，并演示了如何添加用于压缩的 MIME 类型。 有关 ASP.NET Core 1.x 示例，请参阅 ASP.NET Core 1.x [) 的响应压缩示例应用程序 (](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples/1.x)。

## <a name="examples-in-this-sample"></a>此示例中的示例

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum text file response，2044字节，压缩到 ~ 979 个字节。
    * **/testfile1kb.txt** 文本文件响应，以1033字节为单位，压缩为 ~ 36 个字节。
    * **/trickle** -以1秒为间隔的单字符形式发出的响应。
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum text file response，2044字节，压缩到 ~ 927 个字节。
    * **/testfile1kb.txt** 文本文件响应，以1033字节为单位，压缩为 ~ 47 个字节。
    * **/trickle** -以1秒为间隔的单字符形式发出的响应。
  * `image/svg+xml`
    * **/banner.svg** -可缩放的矢量图形 (svg) 在9707字节的图像响应，压缩为 ~ 4459 个字节。
* `CustomCompressionProvider`<br>演示如何实现自定义压缩提供程序以便与中间件一起使用。

如果请求包含 `Accept-Encoding` 标头并且响应压缩成功，则中间件会自动向 `Vary: Accept-Encoding` 响应添加标头。 `Vary`标头指示缓存基于的替代值维护多个响应副本 `Accept-Encoding` ，因此压缩 (Gzip 或 Brotli) 和未压缩版本都存储在可接受压缩或未压缩的响应的系统的缓存中。

## <a name="use-the-sample"></a>使用示例

1. 使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/) 发出请求，使其无 `Accept-Encoding` 标头，并记下响应负载、响应大小和响应标头。
1. 添加 `Accept-Encoding: br` 或 `Accept-Encoding: gzip` 标头，并记下压缩的响应大小和响应标头。 响应大小会下降， `Content-Encoding` 中间件将包含响应标头，指示发生了 Gzip 或 Brotli 的压缩。 查看 Lorem Ipsum 或 **testfile1kb.txt** 响应的响应正文时，会发现文本已压缩且不可读。
1. 添加 `Accept-Encoding: mycustomcompression` 标头并记下响应标头。 `CustomCompressionProvider`是一个空实现，实际上不会压缩响应，但你可以为该方法创建自定义的压缩流包装 `CreateStream()` 。
