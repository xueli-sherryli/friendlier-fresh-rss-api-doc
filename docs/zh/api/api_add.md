# 增加操作

!!! info
    - 所有调用都应在 cookie 中包含 SID（对于 TTRSS 和 FreshRSS 实现，应在请求的 header 中增加`"Authorization": GoogleLogin auth={auth}`）
    - 所有查询类操作都应包含 T token (&T=) (推荐全部传入以防止错误)
    - 除另有说明外，请求地址均为 `~/reader/api/0/` ，如`freshrss.example.net/api/greader.php/reader/api/0/`
    - 推荐保留`client`参数以示不同客户端的区分（`&client=`）(作为params传入)
    - GReader 及其实现将 feed 的 URL 作为其 feed ID，将帖子的 Atom entry ID 作为其 post ID 
    - 在 GReader 及其实现中，文件夹实际上是标签（或 GReader 术语中的 “标签”），这意味着同一 feed 可以存在于多个文件夹中
    - 所有调用都接受一个 `ck=[timestamp]` 参数，根据[Google Reader API 文档](http://code.google.com/p/pyrfeed/wiki/GoogleReaderAPI)的解释，该参数是 “当前的时间戳，可能是用来确保缓存不会被触发的快捷方式”。

!!! Note
    在示例代码中，使用的是同时兼容原版 Google Reader API 和 FreshAPI 的传参方法 (即同时传入 SID 和 header),同时均包含了 t token

## 增加新的订阅源

Endpoint：`subscription/edit`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值                     | 默认值         | 类型   | 说明                                   |
| ------ | ---- | ---------------------- | -------------- | ------ | ------------------------------------- |
| ac     | 是   | subscribe              | 无 | string | 操作类型，固定值为 `subscribe`           |
| s      | 是   | feed/{feed_url}        | 无 | string | `{feed_url}`:欲增加的RSS订阅源链接，使用`feed/`前缀 |
| a      | 否   | user/-/label/{folder} | 无 | string | `{folder}`:欲增加至的RSS分组，使用`user/-/label/`前缀 |
| t      | 否   | {feed_title}           | 与原订阅源相同 | string | `{feed_title}`:欲更改的订阅源名称        |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=subscribe&s=feed/http://example.com/rss&a=user/-/label/Example&t=Example Feed' \
     'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/edit?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/edit'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': YOUR_T_TOKEN
    }
    data = {
        'ac': 'subscribe',
        's': 'feed/http://example.com/rss',
        'a': 'user/-/label/Example',
        't': 'Example Feed'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.post(url, headers=header, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回状态码：

| 状态码 | 解释             |
| ------ | ---------------- |
| 200    | 成功             |
| 其他   | 请参考具体的错误信息 |


## 快速增加新的订阅源

Endpoint：`subscription/quickadd`

Method：`POST`

Data参数：

| 参数名   | 必选 | 示例值              | 默认值 | 类型   | 说明                                     |
| -------- | ---- | --------------- | ------ | ------ | --------------------------------------- |
| quickadd | 是   | feed/{feed_url} |        | string | `{feed_url}`:欲增加的RSS订阅源链接，使用`feed/`前缀 |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'quickadd=feed/http://example.com/rss' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/quickadd?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/quickadd'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'quickadd': 'feed/http://example.com/rss'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.post(url, headers=header, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```


返回状态码：

| 状态码 | 解释             |
| ------ | ---------------- |
| 200    | 成功             |
| 其他   | 请参考具体的错误信息 |


## 创建文件夹

Endpoint：`edit-tag`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值                    | 默认值 | 类型   | 说明                                                                     |
| ------ | ---- | ---------------------- | ------ | ------ | ------------------------------------------------------------------------ |
| ac     | 是   | edit                   |        | string | 操作类型，固定值为`edit`                                                |
| a      | 是   | user/-/label/{folder} |        | string | `{folder}`:  文件夹名称, 使用`user/-/label/`前缀                        |
| s      | 是   | feed/{feed_url}       |        | string | `{feed_url}`:  欲添加到文件夹的RSS订阅源链接, 使用`feed/`前缀, 此处必须提供一个订阅源链接 |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=edit&a=user/-/label/new_folder&s=feed/http://example.com/rss' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'ac': 'edit',
        'a': 'user/-/label/new_folder',
        's': 'feed/http://example.com/rss'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.post(url, headers=header, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
    print(f"An error occurred: {e}")
    ```

返回状态码：

| 状态码 | 解释             |
| ------ | ---------------- |
| 200    | 成功             |
| 其他   | 请参考具体的错误信息 |

**注意:**  必须提供一个`s`参数(RSS订阅源链接)，才能创建文件夹，空文件夹无法创建。


## 将指定文章添加为共享项
!!!info
    对于自部署RSS阅读器，共享项目可能是无用且不被支持的，故不是所有API兼容实现都可以调用该API。 另，`streamId`参数至关重要.


Endpoint：`edit-tag`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值                            | 默认值 | 类型   | 说明                                                                              |
| ------ | ---- | --------------------------------- | ------ | ------ | ------------------------------------------------------------------------------------- |
| a      | 是   | user/-/state/com.google/broadcast |        | string | 操作类型，固定值为 `user/-/state/com.google/broadcast`                             |
| i      | 是   | entry_id                          |        | string | 对应文章的entry_id，可在`<atom>`标签的`<id>`元素中找到                           |
| s      | 是   | stream_id                         |        | string | 对应文章的stream_id，可在`<atom>`标签的`<source>`元素的`gr:stream-id`属性中找到 |
| async  | 是   | true                              |        | bool   | 是否异步执行，固定值为 `true`                                                      |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'a=user/-/state/com.google/broadcast&i=entry_id&s=stream_id&async=true' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'a': 'user/-/state/com.google/broadcast',
        'i': 'entry_id',
        's': 'stream_id',
        'async': 'true'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.post(url, headers=header, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回状态码：

| 状态码     | 解释                 |
| ---------- | -------------------- |
| 200        | 成功                 |
| 其他状态码 | 请参考具体的错误信息 |

“如果没有`s` (streamId)参数，我们也可以查找它，但这会减慢请求速度，并增加失败的几率。 我相信你应该始终掌握这些信息，它在我们 Atom 信息源的 `<source>` 元素中，作为一个`gr:stream-id`命名空间属性。” - Mihai Parparita


## 为文章添加指定标签

Endpoint：`edit-tag`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值             | 默认值 | 类型   | 说明                                                                  |
| ------ | ---- | ------------------ | ------ | ------ | --------------------------------------------------------------------- |
| a      | 是   | user/-/label/{tag} |        | string | `{tag}` 为标签名称，使用`user/-/label/`前缀                           |
| i      | 是   | entry_id           |        | string | 对应文章的entry_id，可在`<atom>`标签的`<id>`元素中找到               |
| async | 否 | true | false | bool | 是否异步操作，默认为false，建议设置为true                            |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'a=user/-/label/tag1&i=entry_id&async=true' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'a': 'user/-/label/tag1',
        'i': 'entry_id',
        'async': 'true'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.post(url, headers=header, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回状态码：

| 状态码     | 解释                 |
| ---------- | -------------------- |
| 200        | 成功                 |
| 其他状态码 | 请参考具体的错误信息 |

## 为文章添加多个标签

Endpoint：`edit-tag`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值                                                                | 默认值 | 类型   | 说明                                                                                       |
| ------ | ---- | ------------------------------------------------------------------- | ------ | ------ | ------------------------------------------------------------------------------------------ |
| a      | 是   | user/-/label/{tag1}&a=user/-/label/{tag2}...                       |        | string | `{tag1} {tag2}` ... 为标签名称，使用`user/-/label/`前缀, 通过重复`a`参数添加多个标签 |
| i      | 是   | entry_id                                                             |        | string | 对应文章的entry_id，可在`<atom>`标签的`<id>`元素中找到                                    |
| async | 否 | true | false | bool | 是否异步操作，默认为false，建议设置为true                                                    |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'a=user/-/label/tag1&a=user/-/label/tag2&a=user/-/label/tag3&i=entry_id&async=true' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag?client=myclient&T=YOUR_T_TOKEN'
    ```
    
=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'a': ['user/-/label/tag1', 'user/-/label/tag2', 'user/-/label/tag3'],
        'i': 'entry_id',
        'async': 'true'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.post(url, headers=header, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回状态码：

| 状态码     | 解释                 |
| ---------- | -------------------- |
| 200        | 成功                 |
| 其他状态码 | 请参考具体的错误信息 |
