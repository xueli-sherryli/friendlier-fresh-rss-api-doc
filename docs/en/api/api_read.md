# Read Method

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

## Mard feed as read

Endpoint: `mark-all-as-read`

Method: `POST`

Data

| parameter  | required | sample value            | default value | type   | description                 |
| ------ | ---- | ------------- | ------ | ------ | -------------------- |
| s      | yes   | feed/{feed_url} | none     | string | feed URL           |
| ts     | yes   | {timestamp}   | none     | number | timestamp               |

Usage：

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

## Mard feed in folder as read

Endpoint: `mark-all-as-read`

Method: `POST`

Data

| parameter  | required | sample value          | default value | type   | description                                   |
| ------ | ---- | ------------- | ------ | ------ | -------------------------------------- |
| t      | yes   | {folder_name} | none     | string | folder name                             |
| ts     | yes   | {timestamp}  | none     | number | timestamp to mark read time |

Usage：

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

## mark single article as read

Endpoint：`edit-tag`

Method： `POST`

Data：

| parameter  | required | sample value                          | default value | type   | description                                               |
| ------ | ---- | --------------------------- | ------ | ------ | -------------------------------------------------- |
| ac     | yes   | edit-tags                   | none     | string | Operation type                                          |
| i      | yes   | {entry_id}                  | none     | string | entry ID                                            |
| a      | yes   | user/-/state/com.google/read | none     | string | mark as read status                                     |
| async  | recommend | true                        | true   | boolean | Whether to operate asynchronously (optional, default is true)                    |
| s      | no   | feed/{feed_url}             | none     | string |  Links to RSS feeds (optional, Google Reader includes this parameter, but it doesn't seem to be necessary) |

Usage：

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

## mark single article as unread

Endpoint: `edit-tag`

Method: `POST`

Data：

| parameter  | required | sample value                          | default value | type   | description                     |
| ------ | ---- | --------------------------- | ------ | ------ | -------------------------- |
| ac     | yes   | edit-tags                   | none     | string | type                 |
| i      | yes   | {entry_id}                  | none     | string | entry ID                 |
| r      | yes   | user/-/state/com.google/read | none     | string | remove read status             |
| async  | recommend  | true                        | true   | boolean |      |

Note：This method does not always work, but is consistent with the same method in RSS Bandit (also not always effective)

Usage：

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

## Mark multiple articles as read

Endpoint: `edit-tag`

Method: `POST`

Data：

| parameter  | required | sample value                                          | default value | type   | description                                                           |
| ------ | ---- | --------------------------------------------- | ------ | ------ | -------------------------------------------------------------- |
| ac     | yes   | edit-tags                                    | none     | string | Operation type                                                       |
| a      | yes   | user/-/state/com.google/read                   | none     | string | mark as read                                                |
| i      | yes   | {entry_id_1}&i={entry_id_2}&i={entry_id_3}... | none     | string | Multiple Entry IDs, concatenate them with `&i=`.                             |
| async  | recommend | true                                        | true   | boolean | Asynchronous or not                                                    |

Usage：

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