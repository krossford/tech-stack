### 什么是 URI ？
URI 的定义在 [RFC 2396](http://www.faqs.org/rfcs/rfc2396.html) 中有详尽的描述。

URI 是 Uniform Resource Indentifier 的缩写。是用来描述物理的或者抽象的资源的唯一标识符。

这三个字单词也正描述了 URI 的特点：

* 形式统一（Uniform）

形式统一带来的好处是，对于各种各样不同的资源，都能有相同的表现形式。各种资源不相同，但在形式上统一，因此可以使用相同的语义进行解释和理解。可以在不影响现存的资源的情况下，出现新的资源。

* 资源（Resource）

任何事情都可以成为资源，可以被标识。

* 标识符（Indentifier）

标识符即是一个对资源的引用。

#### URI 与 URL
URL 是 URI 的子集概念，它表示的是网络上的一个位置

### URI 的结构与属性

这里将结合 Android 中对应的 [Uri](https://developer.android.com/reference/android/net/Uri.html) 类一起讲。

**最基本的结构**是：
`<scheme>:<scheme-specific-part>`
举例说明：
`http://google.com`
其中 scheme 为 `http` ，而 scheme-specific-part 就是 `//google.com`。

但是 URI 并没有规定 scheme-specific-part 必须有什么固定的结构或规范，这完全是取决于 scheme 是什么。

但是**一般的 URI 结构**为：
`<scheme>:<authority><path>?<query>#<fragment>`

上面除了 scheme 是必须的之外，其他的部分都是**可选的**。

#### scheme
资源有多种多样类型，也因此对应了多种多样的访问形式（方法），因此 scheme 就是一种描述**以何种方式访问何种资源**的概念。

常见的有： http, ftp, mailto, file 等等。

scheme 可以通过 `Uri.getScheme()` 获取。

```kotlin
val uri = Uri.parse("http://google.com")
uri.scheme // "http"
```

#### authority

authority是表示用于验证的用户的信息，它的构成形式为
`<user>@<host>:<port>`

举例说明：
`kross@google.com:80`

其中 `kross` 就是 <user> 部分

可以使用 `Uri.getAuthority()` 获取整个 authority，通过 `Uri.getUserInfo()` 获取 user 信息。

这里需要注意，有一些方法是带了 encoded 前缀，它的意思是返回 escaped 内容。看下面的例子就懂了。

```kotlin
val uri = Uri.parse("http://kr%20oss@google.com")
uri.authority // "kr oss@google.com"
uri.encodedAuthority // "kr%20oss@google.com"
uri.userInfo // "kr oss"
```

#### path
简单理解的话， path 是通过单个斜杠 `/` 连接的一个路径，形如： `google.com/article/comment`。

#### query
query 是指在 URI 中，跟在 `?` 后面的部分。换句话说，就是 GET 请求时的参数部分。

如：`http://google.com?keyword=hello&lang=zh_cn`

在上面的示例中，query 为 `keyword=hello&lang=zh_cn` ，这整个一长串。

在 `android.net.Uri` 中，可以使用 `Uri.getQuery()` 和 `Uri.getQueryParameter()` 获取参数。

```kotlin
val uri = Uri.parse("http://google.com?keyword=hello&lang=zh_cn")
uri.getQuery() // "keyword=hello&lang=zh_cn"
uri.getQueryParameter("keyword") // "hello"
```

#### fragment
fragment 是由一个井号 `#` 开始，后面跟着的一串，都为 fragment。

如：`http://google.com#title1`

在上面的示例中 `title1` 为 fragment。

可以使用 `Uri.getFragment()` 获取。

```kotlin
val uri = Uri.parse("http://google.com#abcd#title")
uri.getFragment() // "abcd#title"
```

#### isHierarchical 与 isOpaque
什么叫 isHierarchical 的，当 scheme-specific-part 的部分是由斜杠 `/` 开始的，即这个 URI is Hierarchical。

isOpaque 是与 isHierarchical 相反的概念，即 `isOpaque = !isHierarchical`

举例说明：
HierarchicalURI: `http://google.com` or `http:/google.com`
OpaqueURI: `http:google.com`

#### isAbsolute 与 isRelative
什么叫 isAbsolute ？当 URI 有明确的 scheme 的时候，这个 URI 即为 absolute，反之为 relative，即 `isAbsolute = !isRelative`

举例说明：
AbsoluteURI: `http://google.com`
RelativeURI: `google.com`

#### 其他

为了性能，Uri 类没有做任何验证逻辑。

#### 为什么有的 URI 有三个斜杠？

在 Android 中用 `file:///android_asset/abc.txt` 来表明 Assets 目录中的文件。

所以为什么要用三个斜杠呢？

原因依然回归到本质，URI 的形式为 `<scheme>://<host>/<path>`

如果 <host> 是 localhost，则可以省略。所以，省略掉 <host> 后，就变成了 `<scheme>:///<path>`，这就是三个斜杠的由来。

参考资料：[why-do-file-urls-start-with-3-slashes](https://superuser.com/questions/352133/why-do-file-urls-start-with-3-slashes)

### URI 示例
`ftp://ftp.is.co.za/rfc/rfc1808.txt`
-- ftp scheme for File Transfer Protocol services

`gopher://spinaltap.micro.umn.edu/00/Weather/California/Los%20Angeles`
-- gopher scheme for Gopher and Gopher+ Protocol services

`http://www.math.uio.no/faq/compression-faq/part1.html`
-- http scheme for Hypertext Transfer Protocol services

`mailto:mduerst@ifi.unizh.ch`
-- mailto scheme for electronic mail addresses

`news:comp.infosystems.www.servers.unix`
-- news scheme for USENET news groups and articles

`telnet://melvyl.ucop.edu/`
-- telnet scheme for interactive services via the TELNET Protocol

**更新日志**

2019-12-03 11:32 更新了为什么 URI 有三个斜杠的内容
2019-11-28 首次完成