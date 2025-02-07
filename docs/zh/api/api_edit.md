# 编辑操作

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

## 移动订阅源

Endpoint：`subscription/edit`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值            | 默认值 | 类型   | 说明                                         |
| ------ | ---- | --------------- | ------ | ------ | -------------------------------------------- |
| `ac`     | 是   | `edit`            | 无     | string | 操作类型                                      |
| `s`      | 是   | `feed/{feed_url}` | 无     | string | `{feed_url}`: 欲移动的 RSS 订阅源链接                |
| `r`      | 是   | `user/-/label/{old_folder}` | 无     | string | `{old_folder}`: 订阅源当前所在的文件夹名称，使用`user/-/label/`前缀 |
| `a`      | 是   | `user/-/label/{new_folder}` | 无     | string | `{new_folder}`: 订阅源目标文件夹名称，使用`user/-/label/`前缀 |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=edit&s=feed/http://example.com/rsss&r=user/-/label/old_folder&a=user/-/label/new_folder'  \
     'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/edit?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/edit'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'ac': 'edit',
        's': 'feed/http://example.com/rss',
        'r': 'user/-/label/OldFolder',
        'a': 'user/-/label/NewFolder'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回状态码：

| 状态码 | 解释       |
| ------ | ---------- |
| 200    | 成功       |
| 其他    |  错误，请参考具体的错误信息  |


## 重命名订阅源

Endpoint：`subscription/edit`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值           | 默认值 | 类型   | 说明                               |
| ------ | ---- | -------------- | ------ | ------ | ---------------------------------- |
| `ac`     | 是   | `edit`          | 无     | string | 操作类型                             |
| `s`      | 是   | `feed/{feed_url}` | 无     | string | `{feed_url}`: 欲重命名的 RSS 订阅源链接 |
| `t`      | 是   | `{new_feed_title}`    | 无     | string | `{new_feed_title}`: 新的订阅源名称           |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=edit&s=feed/http://example.com/rss&t=new_feed_title'   \
     'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/edit?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/edit'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'ac': 'edit',
        's': 'feed/http://example.com/rss',
        't': 'new_feed_title'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回状态码：

| 状态码 | 解释       |
| ------ | ---------- |
| 200    | 成功       |
| 其他    | 错误，请参考具体的错误信息 |


## 编辑订阅标签/创建文件夹(如果不存在)

Endpoint：`edit-tag`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值                            | 默认值 | 类型   | 说明                                                            |
| ------ | ---- | -------------------------------- | ------ | ------ | --------------------------------------------------------------- |
| `ac`     | 是   | `edit`                             | 无     | string | 操作类型                                                        |
| `a`      | 是   | `user/-/label/{folder}`             | 无     | string | 用户标签/文件夹路径，`{folder}` 为标签/文件夹名称，使用`user/-/label/`前缀  |
| `s`      | 是   | `feed/{feed_url}`                 | 无     | string | `{feed_url}`：欲添加到该标签的 RSS 订阅源链接，使用`feed/`前缀。**必须提供一个feed，否则无法创建文件夹** |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=edit&a=user/-/label/NewFolder&s=feed/http://example.com/rss \
        'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'ac': 'edit',
        'a': 'user/-/label/NewFolder',
        's': 'feed/http://example.com/rss
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```


返回状态码：

| 状态码 | 解释                                      |
| ------ | ----------------------------------------- |
| 200    | 成功                                      |
| 其他    | 错误，请参考具体的错误信息 |


## 删除文件夹

Endpoint：`disable-tag`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值                            | 默认值 | 类型   | 说明                                                          |
| ------ | ---- | -------------------------------- | ------ | ------ | ------------------------------------------------------------- |
| `ac`     | 是   | `disable-tags`                   | 无     | string | 操作类型                                                       |
| `s`      | 是   | `user/-/label/{folder}`             | 无     | string | 用户标签/文件夹路径，`{folder}` 为文件夹名称，使用`user/-/label/`前缀 |
| `t`      | 是   | `{folder}`                        | 无     | string |  要删除的文件夹名称                                           |

!!! warning
    这只会删除文件夹本身，不会删除其中的订阅源。

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=disable-tags&s=user/-/label/folder&t=folder' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/disable-tag?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/disable-tag'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'ac': 'disable-tags',
        's': 'user/-/label/folder',
        't': 'folder'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回状态码：

| 状态码 | 解释                               |
| ------ | ----------------------------------- |
| 200    | 成功                               |
| 其他    | 错误，请参考具体的错误信息 |


## 取消指定文章共享

!!!info
    对于自部署 RSS 阅读器，共享项目可能是无用且不被支持的，故不是所有 API 兼容实现都可以调用该 API

Endpoint：`edit-tag`

Method：`POST`

参数：

| 参数名 | 必选 | 示例值                               | 默认值 | 类型   | 说明                                         |
| ------ | ---- | ------------------------------------ | ------ | ------ | -------------------------------------------- |
| `r`      | 是   | `user/-/state/com.google/broadcast` | 无     | string | 取消共享操作                                   |
| `i`      | 是   | `{entry_id}`                          | 无     | string | 对应文章的 `{entry_id}`，可在 `<entry>` 标签中找到 |
| `async`  | 否   | `true`                               | `true`  | bool   | 是否异步执行                                 |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'r=user/-/state/com.google/broadcast&i=entry_id&async=true' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/edit-tag'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'r': 'user/-/state/com.google/broadcast',
        'i': 'entry_id',
        'async': 'true',
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回状态码：

| 状态码 | 解释                 |
| ------ | -------------------- |
| 200    | 成功                 |
| 其他    | 错误，请参考具体的错误信息 |



!!! note
    要重命名标签/文件夹，应当删除旧标签/文件夹，然后将新标签/文件夹应用到所有原本带有旧标签/文件夹的文章上

## 改变标签的共享设置

!!! note
    Google Reader 允许共享标签，这意味着可以为每个标签创建一个可公开访问的 HTML 网页和 RSS 订阅源。


Endpoint：`tag/edit`

Method：`POST`

Parmas参数：

| 参数名   | 必选 | 示例值     | 默认值 | 类型   | 说明              |
| -------- | ---- | ---------- | ------ | ------ | ----------------- |
| `client` | 是   | `settings` | 无     | string | 固定值 `settings` |

Data参数：

| 参数名 | 必选 | 示例值               | 默认值 | 类型   | 说明                               |
| ------ | ---- | -------------------- | ------ | ------ | ---------------------------------- |
| `s`      | 是   | `user/-/label/{tag}` | 无     | string | `{tag}`: 对应的标签                 |
| `t`      | 是   | `{tag}`              | 无     | string | `{tag}`: 对应的标签                 |
| `pub`    | 是   | `true` / `false`     | `true`  | bool   | 是否公开发布，`true` 为公开，`false` 为不公开 |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 's=user/-/label/MyTag&t=MyTag&pub=false' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/tag/edit?client=settings&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/tag/edit'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'settings',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        's': 'user/-/label/MyTag',
        't': 'MyTag',
        'pub': 'false'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

!!! note
    共享标签的 HTML 页面地址是：`~/reader/shared/user/{userId}/label/{tag}`，
    而其 RSS 订阅地址是：`~/reader/public/atom/user/{userId}/label/{tag}`。