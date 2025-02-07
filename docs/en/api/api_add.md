!!! info
    - All calls should include the SID in the cookie (for TTRSS and FreshRSS implementations, add `‘Authorisation’: GoogleLogin auth={auth}` to the header of the request)
    - All query-type operations should include the T token (&T=) (recommended to pass all to prevent errors)
    - Unless otherwise specified, request addresses are `~/reader/api/0/`, e.g. `freshrss.example.net/api/greader.php/reader/api/0/`.
    - It is recommended to keep the `client` parameter to show the distinction between different clients (`&client=`) (passed in as params)
    - GReader and its implementation use the URL of a feed as its feed ID and the Atom entry ID of a post as its post ID. 
    - In GReader and its implementation, folders are actually tags (or `labels` in GReader terminology), meaning that the same feed can exist in multiple folders
    - All calls accept a `ck=[timestamp]` parameter, which according to the [Google Reader API documentation](http://code.google.com/p/pyrfeed/wiki/GoogleReaderAPI) is the ‘current timestamp, possibly a shortcut used to ensure that caching is not triggered’.

!!! Note
    In the example code, we are using a pass method that is compatible with both the original Google Reader API and FreshAPI (i.e., we are passing in both the SID and the header), and both include the T token

## Add new subscription feed

Endpoint：`subscription/edit`

Method：`POST`

Data：

| parameter | required | sample value                     | default value         | type   | description              |
| ------ | ---- | ---------------------- | -------------- | ------ | ------------------------------------- |
| ac     | yes   | subscribe              | none | string | operation type, fixed value is `subscribe`.           |
| s      | yes   | feed/{feed_url}        | none | string | `{feed_url}`: the link to the RSS feed to be added, prefixed with `feed/` |
| a      | no   | user/-/label/{folder} | none | string | ``{folder}`: the RSS group you want to add to, prefixed with `user/-/label/` |
| t      | no   | {feed_title}           | Same as original subscription feed | string | `{feed_title}`:The name of the subscription source you want to change        |

usage：

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

Response Code：

| Code | description             |
| ------ | ---------------- |
| 200    | sucess             |
| others   | Please refer to the specific error message |


## Quickly add new subscription feeds

Endpoint：`subscription/quickadd`

Method：`POST`

Data：

| parameter   | required | sample value              | default value | type   | description                                     |
| -------- | ---- | --------------- | ------ | ------ | --------------------------------------- |
| quickadd | yes   | feed/{feed_url} |        | string | `{feed_url}`: the link to the RSS feed to be added, prefixed with `feed/` |

usage：

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


Response Code：

| Code | description             |
| ------ | ---------------- |
| 200    | sucess             |
| others   | Please refer to the specific error message |


## Create new folder

Endpoint：`edit-tag`

Method：`POST`

Data：

| parameter | required | sample value                    | default value | type   | description                                                                     |
| ------ | ---- | ---------------------- | ------ | ------ | ------------------------------------------------------------------------ |
| ac     | yes   | edit                   |        | string | operation type, fixed value is `edit`                                                |
| a      | yes   | user/-/label/{folder} |        | string | `{folder}`: the name of the folder, prefixed with `user/-/label/`                       |
| s      | yes   | feed/{feed_url}       |        | string | `{feed_url}`:  `{feed_url}`: the link to the RSS feed to be added to the folder, prefixed with `feed/`, where a feed link must be provided |

usage：

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

Response Code：

| Code | description             |
| ------ | ---------------- |
| 200    | sucess             |
| others   | Please refer to the specific error message |

**Note:** An `s` parameter (RSS feed link) must be supplied for the folder to be created, an empty folder cannot be created.


## Adding a specific article as a shared item
!!!info
    For self-deploying RSS readers, shared items may be useless and unsupported, so not all API-compatible implementations can call this API. Also, the `streamId` parameter is critical.


Endpoint：`edit-tag`

Method：`POST`

Data：

| parameter | required | sample value               | default value | type   | description                                                                              |
| ------ | ---- | --------------------------------- | ------ | ------ | ------------------------------------------------------------------------------------- |
| a      | yes   | user/-/state/com.google/broadcast |        | string | operation type, fixed value is `user/-/state/com.google/broadcast`                             |
| i      | yes   | entry_id                          |        | string | The `entry_id` of the corresponding article, which can be found in the `<id>` element of the `<atom>` tag                          |
| s      | yes   | stream_id                         |        | string | The `stream_id` of the corresponding article can be found in the `gr:stream-id` attribute of the `<source>` element of the `<atom>` tag |
| async  | yes   | true                              |        | boolean   | yes | Whether to execute asynchronously, the default value is `true`                                                      |

usage：

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

Response Code：

| Code     | description                 |
| ---------- | -------------------- |
| 200        | sucess                 |
| othersCode | Please refer to the specific error message |

‘Without the `s` (streamId) parameter, we could also look it up, but that would slow down the request and increase the chance of failure. I believe you should always have this information, it's in the `<source>` element of our Atom feed as a `gr:stream-id` namespace attribute.’ - Mihai Parparita


## Adding specific tags to an article

Endpoint：`edit-tag`

Method：`POST`

Data：

| parameter | required | sample value             | default value | type   | description                                                                  |
| ------ | ---- | ------------------ | ------ | ------ | --------------------------------------------------------------------- |
| a      | yes   | user/-/label/{tag} |        | string | `{tag}` is the name of the label, prefixed with `user/-/label/`.                             |
| i      | yes   | entry_id           |        | string | The entry_id of the corresponding article, which can be found in the `<id>` element of the `<atom>` tag               |
| async | no | true / false | boolean | yes| Whether the operation is asynchronous, the default is false, it is recommended to set it to true.                             |

usage：

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

Response Code：

| Code     | description                 |
| ---------- | -------------------- |
| 200        | sucess                 |
| othersCode | Please refer to the specific error message |

## Adding multiple tags to a an article

Endpoint：`edit-tag`

Method：`POST`

Data：

| parameter | required | sample value                                                                | default value | type   | description                                                                                       |
| ------ | ---- | ------------------------------------------------------------------- | ------ | ------ | ------------------------------------------------------------------------------------------ |
| a      | yes   | user/-/label/{tag1}&a=user/-/label/{tag2}...                       |        | string | `{tag1} {tag2}` ... For tag names, use `user/-/label/` prefix, add multiple tags by repeating the `a` parameter |
| i      | yes   | entry_id                                                             |        | string | The entry_id of the corresponding article, which can be found in the `<id>` element of the `<atom>` tag                                     |
| async | no | true | false | boolean | Whether the operation is asynchronous, the default is false, it is recommended to set it to true.                                                    |

usage：

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

Response Code：

| Code     | description                 |
| ---------- | -------------------- |
| 200        | sucess                 |
| othersCode | Please refer to the specific error message |
