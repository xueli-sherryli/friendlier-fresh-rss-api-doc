# 搜索操作

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

!!! warning
    注意API端点的变化, `directory/search` 的 API 请求路径中不包含 `/api/0/`

## 搜索 GReader 分类

Endpoint: `directory/search`

Method: `GET`

Params参数:

| 参数名 | 必选 | 值           | 默认值 | 类型   | 说明                     |
| ------ | ---- | ------------- | ------ | ------ | -------------------------- |
| q      | 是   | {search_term} | 无     | string | 搜索关键词               |

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/directory/search?q=searchTerm&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/directory/search'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'q': 'searchTerm',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.get(url, headers=headers, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回结果：HTML 格式的搜索结果页面.


## 搜索项目 ID

Endpoint: `search/items/ids`

Method: `GET`

Params参数:

| 参数名 | 必选 | 值           | 默认值 | 类型   | 说明                                   |
| ------ | ---- | ------------- | ------ | ------ | -------------------------------------- |
| q      | 是   | {search_term} | 无     | string | 搜索关键词                             |
| output | 推荐 | xml/json       | xml    | string | 输出格式 (推荐使用 `json` 以便于处理)  |

用法示例：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/search/items/ids?q=searchTerm&output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/search/items/ids'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'q': 'searchTerm',
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.get(url, headers=headers, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```
    
返回结果：包含匹配搜索词的项目 ID 列表的对象。


## 获取搜索结果内容

Endpoint: `stream/items/contents`

Method: `POST`

Params参数:

| 参数名 | 必选 | 值        | 默认值 | 类型   | 说明                                                         |
| ------ | ---- | --------- | ------ | ------ | ------------------------------------------------------------ |
| output | 推荐 | atom/json | atom   | string | 输出格式. 注意：即使指定了 `atom`，当未明确指定时，此接口也返回 JSON 格式 |

Data参数:

| 参数名 | 必选 | 值                               | 默认值 | 类型   | 说明                                                       |
| ------ | ---- | --------------------------------- | ------ | ------ | ---------------------------------------------------------- |
| i      | 是   | {item_id_1}&i={item_id_2}&...  | 无     | string | 多个项目 ID，使用 `&i=` 连接                               |

用法示例：

=== "Curl"

    ```bash
    curl -s -X POST -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' -d 'i=item_id_1&i=item_id_2&i=item_id_3' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/stream/items/contents?output=json&client=myclient&T=YOUR_T_TOKEN'
    ```
=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/stream/items/contents'
    headers = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    data = {
        'i': ['item_id_1', 'item_id_2', 'item_id_3']
    }
    cookies = {'SID': 'YOUR_SID'}

    try:
        response = requests.post(url, headers=headers, params=params, data=data, cookies=cookies)
        response.raise_for_status()
        print(response.text)
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回结果：指定 ID 的项目内容.  注意：即使指定了 `atom` 输出，如果未明确指定输出格式，该接口返回 JSON 格式。
