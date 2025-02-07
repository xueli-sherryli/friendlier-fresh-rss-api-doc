# 阅读操作

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

## 订阅源标为已读

Endpoint: `mark-all-as-read`

Method: `POST`

data参数

| 参数名 | 必选 | 示例值            | 默认值 | 类型   | 说明                 |
| ------ | ---- | ------------- | ------ | ------ | -------------------- |
| s      | 是   | feed/{feed_url} | 无     | string | 订阅源 URL           |
| ts     | 是   | {timestamp}   | 无     | number | 时间戳               |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 's=feed/http://example.com/rss&ts=1234567890' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/mark-all-as-read?client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/mark-all-as-read'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        's': 'feed/http://example.com/rss',
        'ts': '1234567890'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

## 标记文件夹下所有文章为已读

Endpoint: `mark-all-as-read`

Method: `POST`

data参数

| 参数名 | 必选 | 示例值          | 默认值 | 类型   | 说明                                   |
| ------ | ---- | ------------- | ------ | ------ | -------------------------------------- |
| t      | 是   | {folder_name} | 无     | string | 文件夹名称                             |
| ts     | 是   | {timestamp}  | 无     | number | 时间戳 (可选，用于指定需要标记为已读的文章的时间点) |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 't=ExampleFolder&ts=1234567890' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/mark-all-as-read?client=myclient&T=YOUR_T_TOKEN'
    ```
=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/mark-all-as-read'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        't': 'ExampleFolder',
        'ts': '1234567890'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

## 标记单篇文章为已读

Endpoint：`edit-tag`

Method： `POST`

Data参数：

| 参数名 | 必选 | 示例值                          | 默认值 | 类型   | 说明                                               |
| ------ | ---- | --------------------------- | ------ | ------ | -------------------------------------------------- |
| ac     | 是   | edit-tags                   | 无     | string | 操作类型                                           |
| i      | 是   | {entry_id}                  | 无     | string | 文章 ID                                            |
| a      | 是   | user/-/state/com.google/read | 无     | string | 标记为已读状态                                     |
| async  | 推荐 | true                        | true   | boolean | 是否异步操作 (可选，默认为 true)                   |
| s      | 否   | feed/{feed_url}             | 无     | string |  RSS 订阅源链接 (可选，Google Reader 包含此参数，但似乎并非必要) |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=edit-tags&i=entry_id&a=user/-/state/com.google/read&async=true&s=feed/http://example.com/rss' \
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
        'ac': 'edit-tags',
        'i': 'entry_id',
        'a': 'user/-/state/com.google/read',
        'async': 'true',
        's': 'feed/http://example.com/rss'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

## 标记单篇文章为未读

Endpoint: `edit-tag`

Method: `POST`

Data参数：

| 参数名 | 必选 | 示例值                          | 默认值 | 类型   | 说明                     |
| ------ | ---- | --------------------------- | ------ | ------ | -------------------------- |
| ac     | 是   | edit-tags                   | 无     | string | 操作类型                 |
| i      | 是   | {entry_id}                  | 无     | string | 文章 ID                 |
| r      | 是   | user/-/state/com.google/read | 无     | string | 移除已读状态             |
| async  | 推荐  | true                        | true   | boolean | 是否异步操作 (可选)     |

备注：此方法并非总是有效，但与 RSS Bandit 中的相同方法一致 (同样并非总是有效)。

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=edit-tags&i=entry_id&r=user/-/state/com.google/read&async=true' \
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
        'ac': 'edit-tags',
        'i': 'entry_id',
        'r': 'user/-/state/com.google/read',
        'async': 'true'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

## 标记多篇文章为已读

Endpoint: `edit-tag`

Method: `POST`

Data参数：

| 参数名 | 必选 | 示例值                                          | 默认值 | 类型   | 说明                                                           |
| ------ | ---- | --------------------------------------------- | ------ | ------ | -------------------------------------------------------------- |
| ac     | 是   | edit-tags                                    | 无     | string | 操作类型                                                       |
| a      | 是   | user/-/state/com.google/read                   | 无     | string | 标记为已读状态                                                 |
| i      | 是   | {entry_id_1}&i={entry_id_2}&i={entry_id_3}... | 无     | string | 多个 Entry ID，用 `&i=` 连接                             |
| async  | 推荐 | true                                        | true   | boolean | 是否异步操作                                                   |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=edit-tags&a=user/-/state/com.google/read&i=entry_id_1&i=entry_id_2&i=entry_id_3&async=true' \
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
        'ac': 'edit-tags',
        'a': 'user/-/state/com.google/read',
        'i': ['entry_id_1', 'entry_id_2', 'entry_id_3'],
        'async': 'true'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```