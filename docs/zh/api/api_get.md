# 获取操作

!!! info
    - 所有调用都应在 cookie 中包含 SID（对于 TTRSS 和 FreshRSS 实现，应在请求的 header 中增加`"Authorization": GoogleLogin auth={auth}`）
    - 所有查询类操作都应包含 T token  (&T=) (推荐全部传入以防止错误)
    - 除另有说明外，请求地址均为 `~/reader/api/0/` ，如`freshrss.example.net/api/greader.php/reader/api/0/`
    - 推荐保留`client`参数以示不同客户端的区分（`&client=`）(作为params传入)
    - GReader 及其实现将 feed 的 URL 作为其 feed ID，将帖子的 Atom entry ID 作为其 post ID 
    - 在 GReader 及其实现中，文件夹实际上是标签（或 GReader 术语中的 “标签”），这意味着同一 feed 可以存在于多个文件夹中
    - 所有调用都接受一个 `ck=[timestamp]` 参数，根据[Google Reader API 文档](http://code.google.com/p/pyrfeed/wiki/GoogleReaderAPI)的解释，该参数是 “当前的时间戳，可能是用来确保缓存不会被触发的快捷方式”。

!!! Note
    在示例代码中，使用的是同时兼容原版 Google Reader API 和 FreshAPI 的传参方法 (即同时传入 SID 和 header) 同时均包含了 t token
    
## 获取订阅列表

Endpoint：`subscription/list`

Method：`GET`

params参数：

| 参数名 | 必选 | 示例值       | 默认值 | 类型   | 说明           |
| ------ | ---- | ------------ | ------ | ------ | -------------- |
| output | 是   | `json`/`xml` | `xml`  | string | 选择输出的格式 |

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/list?output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

当 `output=xml`时，返回样例如下

```xml
<object> 
<string name="id">feed/http://feeds.feedburner.com/NickBradbury</string>  
<string name="title">Nick Bradbury</string>  
<list name="categories"> 
<object> 
<string name="id">user/[USER_ID]/label/Bradbury Software</string>  
<string name="label">Bradbury Software</string>  
</object> 
</list> 
<string name="sortid">C430EC8F</string>  
<number name="firstitemmsec">1203460831522</number>  
</object> 
```

当`output=json`时，返回样例如下

```json
{
	"subscriptions": [{
		"id": "feed/39",
		"title": "标题测试1",
		"categories": [{
			"id": "user/-/label/Uncategorized",
			"label": "Uncategorized"
		}],
		"url": "https://example.com",
		"htmlUrl": "https://example-html.com",
		"iconUrl": "http://example/public.php?op=feed_icon&id=39.ico"
	}, {
		"id": "feed/31",
		"title": "标题测试2",
		"categories": [{
			"id": "user/-/label/分类测试1",
			"label": "分类测试1"
		}],
		"url": "https://example.com",
		"htmlUrl": "https://example-html.com",
		"iconUrl": "http://example/public.php?op=feed_icon&id=31.ico"
	}, {
		"id": "feed/30",
		"title": "标题测试3",
		"categories": [{
			"id": "user/-/label/分类测试1",
			"label": "分类测试1"
		}],
		"url": "https://example.com",
		"htmlUrl": "https://example-html.com",
		"iconUrl": "http://example/public.php?op=feed_icon&id=30.ico"
	}]
}
```
!!! info
    文件夹的名称以 <string name="label"> 的形式存储在 <list name="categories"> 节点中。 根文件夹中的馈送有一个空的<list name="categories"/> 节点。 firstitemmsec 属性是该用户订阅中最旧（第一个）项目的时间戳。
    
    更简单的方法是以 GET 方法调用 `subscriptions/export` 以 获取OPML文件的导出 ，但这不包括任何 GReader 数据（如 firstitemmsec）。
    
    **注意:**  请求参数中的 `/` 需要进行 URL 编码，例如 `/` 编码为 `%2F`。
    
    **注意:**  除非传入feed参数（s参数），否则操作会失败。(无法创建空文件夹，必须创建有订阅源的文件夹)


## 获取阅读器总未读计数

Endpoint：`unread-count`

Method：`GET`

params参数：

| 参数名 | 必选 | 示例值  | 默认值 | 类型   | 说明                                                         |
| ------ | ---- | ------ | ------ | ------ | ------------------------------------------------------------ |
| output | 是   | `xml`/`json` | `xml`  | string | 指定返回格式为XML                                             |

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/unread-count?output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/unread-count'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

如果存在未读条目，返回`xml`样例如下

```xml
<object> 
    <number name="max">1000</number> 
    <list name="unreadcounts"> 
        <object> 
            <string name="id">user/-/state/com.google/reading-list</string> 
            <number name="count">8</number> 
            <number name="newestItemTimestampUsec">1232641608774062</number> 
        </object> 
        <object> 
            <string name="id">feed/http://rss.cnn.com/rss/cnn_topstories.rss</string> 
            <number name="count">4</number> 
            <number name="newestItemTimestampUsec">1232641203555343</number> 
        </object> 
        <object> 
            <string name="id">user/-/label/FolderName</string> 
            <number name="count">8</number> 
            <number name="newestItemTimestampUsec">1232641608774062</number> 
        </object> 
        <object> 
            <string name="id">feed/http://www.engadget.com/rss.xml</string> 
            <number name="count">4</number> 
            <number name="newestItemTimestampUsec">1232641608774062</number> 
        </object> 
</list> 
</object> 
```
返回`json`样例如下

```json
{
    "max": 8
    "unreadcounts": [
        {
            "id": "user/-/state/com.google/reading-list",
            "count": 8,
            "newestItemTimestampUsec": "1232641608774062"
        },
        {
            "id": "feed/http://rss.cnn.com/rss/cnn_topstories.rss",
            "count": 4,
            "newestItemTimestampUsec": "1232641203555343"
        },
                {
            "id": "user/-/label/FolderName",
            "count": 8,
            "newestItemTimestampUsec": "1232641608774062"
        },
        {
            "id": "feed/http://www.engadget.com/rss.xml",
            "count": 4,
            "newestItemTimestampUsec": "1232641608774062"
        }
        ...
    ]
}
```

如果不存在未读条目，返回`xml`样例如下

```xml
<object> 
    <number name="max">1000</number> 
    <list name="unreadcounts"/> 
</object> 
```

返回`json``样例如下

```json	
{
  "max": 1000,
  "unreadcounts": []
}
```



## 获取所有条目

Endpoint：`stream/contents/user/-/state/com.google/reading-list`

Method：`GET`

params参数：

| 参数名 | 必选 | 示例值                       | 默认值 | 类型   | 说明                                                         |
| ------ | ---- | ---------------------------- | ------ | ------ | ------------------------------------------------------------ |
| n      | 否   | 20                           | 20     | number | 请求最大条数，最大为1000                                     |
| r      | 否   | o / n                        | o      | string | 排序方式。o 表示按文章发布时间倒序排列（旧->新）, n 表示按文章更新时间倒序排列（新->旧） |
| t      | 否   | 1678886400                   | 无     | number | 获取比指定时间戳*更新*的文章（Unix时间戳，毫秒）             |
| ot     | 否   | 1678886400                   | 无     | number | 获取比指定时间戳*更旧*的文章（Unix时间戳，毫秒）             |
| xt     | 否   | user/-/state/com.google/read | 无     | string | 排除标签，示例为排除已读内容                                 |
| c      | 否   | continuation string          | 无     | string | 续读的字符串，用于分页加载更多条目                           |
| output | 是   | xml/json                     | json   | string | 输出样例，默认为json                                         |

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/stream/contents/user/-/state/com.google/reading-list?n=20&r=o&ot=1678886400&xt=user/-/state/com.google/read&output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/stream/contents/user/-/state/com.google/reading-list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'n': 20,
        'r': 'o',
        'ot': 1678886400,
        'xt': 'user/-/state/com.google/read',
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回样例：



## 获取用户信息

Endpoint：`user-info`

Method：`GET`

参数：无

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/user-info?T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/user-info'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params ={
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回样例:

```json
{
    "userId":"admin",
    "userName":"admin",
    "userProfileId":"admin",
    "userEmail":""
}
```

## 获取用户好友信息

Endpoint：`friend/list`

Method：`GET`

参数：无

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/friend/list&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/friend/list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params ={
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```


## 获取阅读器偏好设置信息

Endpoint：`preference/list`

Method：`GET`

参数：无

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/preference/list&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/preference/list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params ={
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

## 获取标星文章

Endpoint:`preference/list/stream/contents/user/-/state/com.google/starred`

Method：`GET`

parmas参数:

| 参数名 | 必选 | 示例值   | 默认值 | 类型   | 说明                          |
| ------ | ---- | -------- | ------ | ------ | ----------------------------- |
| n      | 是   | 20       | 无     | number | 获取星标文章的数量,最大为1000 |
| output | 否   | json/xml | json   | string | 指定返回格式为json或者xml     |

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/stream/contents/user/-/state/com.google/starred?n=20&output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/stream/contents/user/-/state/com.google/starred'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'n': 20,
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回样例

```JSON
{
  "id": "user/-/state/com.google/reading-list",
  "updated": 1738469151,
  "items": [
    {
      "id": "tag:google.com,2005:reader/item/000000000001035c",
      "crawlTimeMsec": "1738411626000",
      "timestampUsec": "1738411626000000",
      "published": 1738411626,
      "title": "...",
      "canonical": [
        {
          "href": "...""
        }
      ],
      "alternate": [
        {
          "href": "...""
        }
      ],
      "categories": [
        "user/-/state/com.google/read",
        "user/-/state/com.google/starred"
      ],
      "origin": {
        "streamId": "feed/31",
        "title": "..."",
        "htmlUrl": "...""
      },
      "summary": {
        "content": "...""
      },
      "author": ""
    }
  ],
  "continuation": "1"
}
```



## 订阅源解析

Endpoint：`/atom/feed/{feed_url}`

Method：`GET`

parmas参数:

| 参数名   | 必选 | 示例值                                   | 默认值 | 类型   | 说明               |
| -------- | ---- | ---------------------------------------- | ------ | ------ | ------------------ |
| feed_url | 是   | http://feeds.feedburner.com/NickBradbury | 无     | string | 欲解析的RSS链接    |
| n        | 否   | 40                                       | 20     | number | 加载文章条目的数量 |
| xt       | 否   | user/-/state/com.google/read             | 无     | string | 排除的标签         |

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/atom/feed/http://feeds.feedburner.com/NickBradbury?n=40&xt=user/-/state/com.google/read&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/atom/feed/http://feeds.feedburner.com/NickBradbury'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'n': 40,
        'xt': 'user/-/state/com.google/read',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回样例:

```xml
<entry>
  <id>tag:google.com,2005:reader/item/30fd6c8e007ebcbf</id>
  <published>2009-01-03T18:37:04Z</published>
  <updated>2009-01-03T18:37:04Z</updated>
  <title>[Article summary]</title>
  <content type="html">[Article content]</content>
  <author>
    <name>Nick Bradbury</name>
  </author>
  <link rel="alternate" href="http://example.com/article" />
  <category scheme="http://www.google.com/reader/atom" term="tag:google.com,2005:reader/feed/http://feeds.feedburner.com/NickBradbury" />
  <gr:crawl-timestamp-msec>1233716224000</gr:crawl-timestamp-msec>  <!-- Unix timestamp -->
  <gr:is-read-state-locked>true</gr:is-read-state-locked> <!-- 如果条目被锁定为未读 -->
</entry>
```
!!! note
     **`gr:crawl-timestamp-msec`** 表示 Google Reader 接收文章的 Unix 时间戳 (毫秒)。
    

     **`gr:is-read-state-locked="true"`**:  如果条目的阅读状态被锁定 ("保持未读")，则会包含此属性。
    
     **`<id>`**条目的值始终为 Google Reader 中的 ID，用以替换订阅源中的原始 GUID/ID。 原始 GUID/ID 仍可从 ID 节点获取,例如:
     
     `<id gr:original-id="http://nick.typepad.com/blog/2009/01/">tag:google.com,2005:reader/item/30fd6c8e007ebcbf</id>`
    
    **`Categories`** 不仅用于反映实际信息源中为该条目分配的类别，还用于反映 GReader 中的已读/标记状态。 
    
    * 如果一篇文章已被阅读,它的**`Categories`**会如下所示：
        * ```<category term="user/[userid]/state/com.google/read" scheme="http://www.google.com/reader/" label="read" />```
    * 如果一篇文章已被标星,它的**`Categories`**会如下所示：
        * ```<category term="user/[userid]/state/com.google/starred" scheme="http://www.google.com/reader/" label="starred"/> ```
    * 如果一篇文章在文件夹内,它的**`Categories`**会如下所示
        * ```<category term="user/[userid]/label/[folderName]" scheme="http://www.google.com/reader/" label="[folderName]"/> ```

Google Reader (GReader) 获取订阅源的一个重要部分是**续读（continuation）**功能，它提供了一种分页浏览订阅源条目（entries）的方法。例如，如果您请求订阅源中最新的 20 条条目，根元素可能包含一个 **`gr:continuation`**子元素，例如：

```xml
<feed xmlns:gr="http://www.google.com/schemas/reader/atom/" xmlns="http://www.w3.org/2005/Atom"> 
    <generator uri="http://www.google.com/reader">Google Reader</generator> 
    <id>tag:google.com,2005:reader/feed/http://feeds.feedburner.com/NickBradburyClippings</id> 
    <title>Nick Bradbury&amp;#39;s Shared Items on NewsGator Online</title> 
    <updated>2009-01-15T15:17:16Z</updated> 
<gr:continuation>CP__hN2fq5gC</gr:continuation>  
```

只需将 gr:continuation 值附加到后续请求中，即可获取订阅源中的接下来的 20 个条目，例如：
GET http://www.google.com/reader/atom/feed/http://feeds.feedburner.com/NickBradbury&c=CP__hN2fq5gC 
这使得访问订阅源的历史记录成为可能，这在订阅订阅源后或用户首次选择启用 GReader 同步时可能立即有用。

## 获取用户共享项

!!!info
    对于自部署RSS阅读器，共享项目可能是无用且不被支持的，故不是所有API兼容实现都可以调用该API

Endpoint：`/reader/atom/user/-/state/com.google/broadcast&n={count}`

Method：`GET`

parmas参数：

| 参数名  | 必选 | 示例值 | 默认值 | 类型   | 说明                     |
| ------- | ---- | ------ | ------ | ------ | ------------------------ |
| {count} | 是   | 20     | 无     | number | 欲获取用户共享文章的数量 |

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/reader/atom/user/-/state/com.google/broadcast?n=20&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/reader/atom/user/-/state/com.google/broadcast'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'n': 20,
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

!!!note

    - 需要注意的是，条目 ID 必须是 GReader 中条目的 ID，因此来自非 GReader 源的条目需要像任意 URL 一样共享，例如：
    - POST item/edit?share=true&title={title}&url={url}&snippet={snippet}&srcTitle="{srcTitle}"&srcUrl={srcUr}。
    - 再次检索用户的共享项目时，您会看到 GReader 已为此项目分配了一个条目 ID。sourceTitle 和 srcUrl 参数分别是项目来源页面/源的名称和链接。还可以添加 annotation 参数。在已添加批注的共享项目条目（“带笔记共享”）中，将会包含类似这样的内容

## 获取标签(文件夹)列表

Endpoint：`tag/list`

Method：`GET`

parmas参数：

| 参数名 | 必选 | 示例值   | 默认值 | 类型   | 说明       |
| ------ | ---- | -------- | ------ | ------ | ---------- |
| output | 是   | xml/json | 无     | string | 输出的格式 |

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/tag/list?output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/tag/list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'output': "json",
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")

如果`output=json`，返回样例如下：

```json
{
	"tags": [{
		"id": "user/-/state/com.google/starred"
	}, {
		"id": "user/-/label/A",
		"type": "folder"
	}, {
		"id": "user/-/label/B",
		"type": "folder"
	}, {
		"id": "user/-/label/C",
		"type": "folder"
	}, {
		"id": "user/-/label/D",
		"type": "folder"
	}, {
		"id": "user/-/label/E",
		"type": "folder"
	}, {
		"id": "user/-/label/F",
		"type": "tag"
	}, {
		"id": "user/-/label/G",
		"type": "folder"
	}, {
		"id": "user/-/label/H",
		"type": "tag"
	}, {
		"id": "user/-/label/I",
		"type": "folder"
	}, {
		"id": "user/-/label/J",
		"type": "folder"
	}, {
		"id": "user/-/label/K",
		"type": "folder"
	}, {
		"id": "user/-/label/L",
		"type": "tag"
	}, {
		"id": "user/-/label/Uncategorized",
		"type": "folder"
	}, {
		"id": "user/-/label/测试",
		"type": "tag"
	}]
}
```

!!! note
    由于 Google Reader 将文件夹视为标签，因此此列表将包含文件夹名称。客户端需要自行解析出实际的文件夹名称标签。

  