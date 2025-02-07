# Authentication

!!! info
    Operations on the FreshRSS / Google Reader API require a SID (Session ID) and a T token.

!!! info
    Unless otherwise specified, requests are addressed to `~/api/greader.php`, e.g. `freshrss.example.net/api/greader.php`.  

## Get SID / LSID / Auth

Endpoint：`/accounts/ClientLogin`

Method： `GET`/`POST`

header:

```
Content-type: application/x-www-form-urlencoded
```

parmas：

!!! Warning
    Please note the difference from the original Google Reader API.

| parameter      | required | sample value     | default value                                 | type   | instruction |
| ----------- | ---- | ------ | ---------------------------------------- | ----------- | ----------- |
| accountType | No  | GOOGLE     | None                                     | string | Account type, fixed value `GOOGLE` |
| Email     | Yes   | `alice` | None                                     | string | User name when logging into the backend |
| Passwd      | Yes   | `Abcdef123456` | None                                     | string | login password |
| service     | No   | string | Service type, fixed value `reader`                 | string | Service type, fixed value `reader` |
| source     | No | string | Source identification, recommended format `[application name]-[version number]`  | string | Source identification, recommended format `[application name]-[version number]` |

Usage：
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

Response：

```
SID={Email}/abcdefghijklmn
LSID={May blank}
Auth={Email}/abcdefghijklmn
```

!!! Note "Notice"
    URL encoding of the login information is required.


## Get T token

Endpoint：`/reader/api/0/token`

Method：`GET`

!!! Warning 
    Note the difference from the original Google Reader API, the Fresh API is implemented differently here.

Header：

| parameter        | required | sample value                                                       | default value | type | instruction |
| ------ | ---- | ------ | ---------------------- | ------ | ------ |
| Authorization | Yes   | GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8 | None | string | pervious `Auth` value |

Usage：

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

Response：

```
abcdefg……  (T token value )
```




## Next

When the SID and T token are obtained, the API can be manipulated; the SID remains valid until logout, and the T token may expire frequently, which can be confirmed by checking the `X-Reader-Google-Bad-Token: true` in the header of the response.