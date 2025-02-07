# 身份鉴权

!!! info
    对 FreshRSS / Google Reader API 的操作需要取得一个 SID（Session ID）和 T token

!!! info
    如无特殊说明，请求地址均为 `~/api/greader.php`， 如`freshrss.example.net/api/greader.php` 

## 获取 SID / LSID / Auth

Endpoint：`/accounts/ClientLogin`

Method： `GET`/`POST`

Headers参数:

```
Content-type: application/x-www-form-urlencoded
```

Parmas参数：

!!! Warning 警告
    请注意与原版Google Reader API的不同

| 参数名      | 必选 | 示例值     | 默认值                                 | 类型   | 说明 |
| ----------- | ---- | ------ | ---------------------------------------- | ----------- | ----------- |
| accountType | 否  | GOOGLE     | 无                                     | string | 账户类型，固定值 `GOOGLE` |
| Email     | 是   | `alice` | 无                                     | string | 登录后端时的用户名 |
| Passwd      | 是   | `Abcdef123456` | 无                                     | string | 登录密码 |
| service     | 否   | string | 服务类型, 固定值 `reader`                 | string | 服务类型, 固定值 `reader` |
| source     | 否 | string | 来源标识，建议格式 `[应用名]-[版本号]`  | string | 来源标识，建议格式 `[应用名]-[版本号]` |

用法示例：
===  "Curl"

	```curl
	curl 'https://freshrss.example.net/api/greader.php/accounts/ClientLogin?	Email=alice&Passwd=Abcdef123456'
	```

===  "Python"

    ```python
    import requests
    url = 'https://freshrss.example.net/api/greader.php/accounts/ClientLogin'
    params = {
        'Email': 'alice',
        'Passwd': 'Abcdef123456'
    }
    try:
        response = requests.get(url, params=params)
        response.raise_for_status()  # Raise HTTPError for bad responses (4xx or 5xx)
        print(response.text)  # Print the response content
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回：

```
SID={Email}/abcdefghijklmn
LSID={可为空}
Auth={Email}/abcdefghijklmn
```

!!! Note "注意"
    需要对登录信息进行 URL 编码。


## 获取 T token

Endpoint：`/reader/api/0/token`

Method：`GET`

!!! Warning 警告
    请注意与原版Google Reader API的不同，Fresh API 此处实现有区别

Header 参数：

| 参数名        | 必选 | 示例值                                                       | 默认值 | 类型 | 说明 |
| ------ | ---- | ------ | ---------------------- | ------ | ------ |
| Authorization | 是   | GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8 | 无 | string | 先前获取的auth值 |

用法示例：

=== "Curl"
    ``` bash
    curl -H "Authorization:GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8" \
      'https://freshrss.example.net/api/greader.php/reader/api/0/token'
    ```
=== "Python"
    ``` python
    import requests
    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/token'
    header = {
    "Authorization:GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"}
    try:
        response = requests.get(url, header=header)
        response.raise_for_status()  # Raise HTTPError for bad responses (4xx or 5xx)
        print(response.text)  # Print the response content

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

返回：

```
abcdefg……  (T token的值)
```




## 下一步

当获取到  SID 和 T token 时，便可以对 API 进行操作。SID 保持至登出前有效，T token 可能频繁过期，可以通过检查返回的 header 中的 `X-Reader-Google-Bad-Token: true` 以确认过期的 token。