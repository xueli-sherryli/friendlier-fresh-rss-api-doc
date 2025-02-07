# 删除操作

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

!!! Warning
    根据 FreshRSS API 示例，以下两个接口传入的内容为 `{feed_id}` 与 Google Reader API 文档中的 `{feed_url}` 不同

## 删除订阅源

Endpoint：`subscription/edit`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值      | 默认值                   | 类型   | 说明 |
| ------ | ---- | --------------- | ------------------------------------- | ------ | ------ |
| ac     | 是   | unsubscribe     | 操作类型，此处为取消订阅               | string | 操作类型，此处为取消订阅 |
| s      | 是 | {feed_id}   | 无 | string | {feed_id}:欲删除的 RSS 订阅源id,必须以feed/开头,示例：`feed/52` |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=unsubscribe&s=feed/52' \
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
        'ac': 'unsubscribe',
        's': 'feed/52'
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

| 状态码 | 说明 |
| ------ | ---- |
| 200    | 成功 |
| 其他   | 请参考具体的错误信息 |


## 删除多个订阅源

Endpoint：`subscription/edit`

Method：`POST`

该接口支持一次删除多个订阅源。  通过多次使用 `s` 参数，每个 `s` 参数指定一个要删除的订阅源 id。

Data参数：


| 参数名 | 必选 | 示例值      | 默认值 | 类型   | 说明 |
| ------ | ---- | --------------- | ----------------------------------------------------- | ------ | ------ |
| ac     | 是   | unsubscribe     | 无                              | string | 操作类型，此处为取消订阅 |
| s      | 是   | {feed_id}   | 无 | string | {feed_id}:欲删除的 RSS 订阅源id,必须以feed/开头,示例：`feed/52` |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=unsubscribe&s=feed/11&s=feed/52' \
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
        'ac': 'unsubscribe',
        's': ['feed/http%3A%2F%2Fexample.com%2Frss', 'feed/http%3A%2F%2Fexample2.com%2Frss']
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

| 状态码 | 说明 |
| ------ | ---- |
| 200    | 成功 |
| 其他   | 请参考具体的错误信息 |



## 删除文件夹

Endpoint：`disable-tag`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值                       | 默认值       | 类型   | 说明 |
| ------ | ---- | ----------------- | ------------------------------------------------------------ | ------ | ------ |
| ac     | 是   | disable-tags      | disable-tags                                          | string | 固定值，操作类型 |
| s      | 是   | `user/-/label/{folder_name}` | 无 | string | `{folder_name}`: 欲删除的文件夹名称，必须以 `user/-/label/` 为前缀。 |
| t      | 是   | `{folder_name}`    | 无         | string | `{folder_name}`: 欲删除的文件夹名称（与 s 参数值相同） |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'ac=disable-tags&s=user/-/label/Example&t=Example' \
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
        's': 'user/-/label/Example',
        't': 'Example'
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

| 状态码 | 说明 |
| ------ | ---- |
| 200    | 成功 |
| 其他   | 请参考具体的错误信息 |

!!! note
    此操作不会删除文件夹内的订阅源，只会删除文件夹本身。


## 从文章中删除指定标签

Endpoint：`edit-tag`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值              | 默认值                                                             | 类型 | 说明 |
| ------ | ---- | ------------------ | ------------------------------------------------------------------- | ------ | ------ |
| r      | 是   | `user/-/label/{tag_name}` | user/-/label/{tag_name} | string | `{tag_name}`: 欲删除的标签名称，必须以 `user/-/label/` 为前缀。 |
| i      | 是   | `{entryId}`         | 无 | string | `{entryId}`: 对应文章的 Entry ID，可以在 Atom  feed 的 `<entry>` 标签中找到。 |
| async  | 建议 | true               | 无                      | boolean | 是否异步执行 |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'r=user/-/label/Example&i=entry_id&async=true' \
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
        'r': 'user/-/label/Example',
        'i': 'entry_id',
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

返回状态码：

| 状态码 | 说明 |
| ------ | ---- |
| 200    | 成功 |
| 其他   | 请参考具体的错误信息 |



## 从所有文章中移除标签

Endpoint：`disable-tag`

Method：`POST`

Data参数：

| 参数名 | 必选 | 示例值                    | 默认值 | 类型 | 说明 |
| ------ | ---- | ----------------- | ----------------------------------------------------------- | ------ | ------ |
| s      | 是   | `user/-/label/{tag_name}` | 无 | string | `{tag_name}`: 欲删除的标签名称，必须以 `user/-/label/` 为前缀。 |
| t      | 是   | `{tag_name}`        | 无                              | string | `{tag_name}`: 欲删除的标签名称 |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 's=user/-/label/Example&t=Example' \
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
        's': 'user/-/label/Example',
        't': 'Example'
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

| 状态码 | 说明 |
| ------ | ---- |
| 200    | 成功 |
| 其他   | 请参考具体的错误信息 |
