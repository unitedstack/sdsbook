Swift Restful API 提供了基于 Http Restful 规范的访问方式，用户可以通过向服务器端发送 Http 请求来访问对象存储资源，实现权限认证，存储桶操作，对象操作等。需要注意的是，通过 Http 传输数据是不可靠的，传输或者请求过程中，可能发生超时，链接失败等情况，这取决于网络质量以及服务器端当前负载压力。故在操作过程中，用户需要自行处理 Http 请求错误码，以保证操作能够进行重试，重传等操作，最终保证操作能够完成。除此之外，如果进行 Http 请求时使用长连接，那么对连接进行复用，否则可能导致服务端连接数达到上限而无法继续响应请求。

关于 Swift Restful API 的使用，请参见：[Swift Restful API](http://developer.openstack.org/api-ref/object-storage/?expanded=)
