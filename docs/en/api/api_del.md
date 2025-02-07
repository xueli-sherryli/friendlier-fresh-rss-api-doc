# Delete Method

!!! info
    - All calls should include the SID in the cookie (for TTRSS and FreshRSS implementations, add `‘Authorisation’: GoogleLogin auth={auth}` to the header of the request)
    - All query-type operations should include the T token (&T=) (recommended to pass all to prevent errors)
    - Unless otherwise specified, request addresses are `~/reader/api/0/`, e.g. `freshrss.example.net/api/greader.php/reader/api/0/`.
    - It is recommended to keep the `client` parameter to show the distinction between different clients (`&client=`) (passed in as params)
    - GReader and its implementation use the URL of a feed as its feed ID and the Atom entry ID of a post as its post ID. 
    - In GReader and its implementation, folders are actually tags (or `labels` in GReader terminology), meaning that the same feed can exist in multiple folders
    - All calls accept a `ck=[timestamp]` parameter, which according to the [Google Reader API documentation](http://Code.google.com/p/pyrfeed/wiki/GoogleReaderAPI) is the ‘current timestamp, possibly a shortcut used to ensure that caching is not triggered’.

!!! Note
    In the example Code, we are using a pass method that is compatible with both the original Google Reader API and FreshAPI (i.e., we are passing in both the SID and the header), and both include the T token

!!! Warning
    According to the FreshRSS API example, the following two interfaces pass in `{feed_id}` which is different from the `{feed_url}` in the Google Reader API documentation.

## Delete Subscription

Endpoint：`subscription/edit`

Method：`POST`

Data：

| parameter | required | sample value      | default value                   | type   | description |
| ------ | ---- | --------------- | ------------------------------------- | ------ | ------ |
| ac     | yes   | unsubscribe     | Action type, in this case unsubscribe               | string | Action type, in this case unsubscribe |
| s      | yes | {feed_id}   | none | string | {feed_id}:The id of the RSS feed you want to delete must start with feed/, example: `feed/52` |

Usage：

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

Code：

| Code | description |
| ------ | ---- |
| 200    | sucess |
| other   | Please refer to the specific error message |


## Deleting multiple subscriptions

Endpoint：`subscription/edit`

Method：`POST`

This interface supports deleting multiple subscription sources at once.  By using the `s` parameter multiple times, each `s` parameter specifies a subscription source id to be deleted.

Data：


| parameter | required | sample value      | default value | type   | description |
| ------ | ---- | --------------- | ----------------------------------------------------- | ------ | ------ |
| ac     | yes   | unsubscribe     | none                              | string | Action type, in this case unsubscribe |
| s      | yes   | {feed_id}   | none | string | {feed_id}:The id of the RSS feed you want to delete must start with feed/, example: `feed/52`. |

Usage：

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

Code：

| Code | description |
| ------ | ---- |
| 200    | sucess |
| other   | Please refer to the specific error message |



## Delete Folder

Endpoint：`disable-tag`

Method：`POST`

Data：

| parameter | required | sample value                       | default value       | type   | description |
| ------ | ---- | ----------------- | ------------------------------------------------------------ | ------ | ------ |
| ac     | yes   | disable-tags      | disable-tags                                          | string | Fixed value, operation type |
| s      | yes   | `user/-/label/{folder_name}` | none | string | `{folder_name}`: The name of the folder to be deleted must be prefixed with `user/-/label/` |
| t      | yes   | `{folder_name}`    | none         | string | `{folder_name}`: Name of the folder to be deleted (same value as the s parameter) |

Usage：

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

Code：

| Code | description |
| ------ | ---- |
| 200    | sucess |
| other   | Please refer to the specific error message |

!!! note
    This action does not delete the subscription feeds within the folder, only the folder itself.


## Remove specified tags from article

Endpoint：`edit-tag`

Method：`POST`

Data：

| parameter | required | sample value              | default value                                                             | type | description |
| ------ | ---- | ------------------ | ------------------------------------------------------------------- | ------ | ------ |
| r      | yes   | `user/-/label/{tag_name}` | user/-/label/{tag_name} | string | `{tag_name}`: The name of the label to be deleted must be prefixed with `user/-/label/` |
| i      | yes   | `{entryId}`         | none | string | `{entryId}`: The Entry ID of the corresponding article can be found in the `<entry>` tag of the Atom feed |
| async  | 建议 | true               | none                      | boolean | Whether to execute asynchronously |                         |

Usage：

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

Code：

| Code | description |
| ------ | ---- |
| 200    | sucess |
| other   | Please refer to the specific error message |



## Remove tags from all articles

Endpoint：`disable-tag`

Method：`POST`

Data：

| parameter | required | sample value                    | default value | type | description |
| ------ | ---- | ----------------- | ----------------------------------------------------------- | ------ | ------ |
| s      | yes   | `user/-/label/{tag_name}` | none | string | `{tag_name}`: The name of the label to be deleted must be prefixed with `user/-/label/`. |
| t      | yes   | `{tag_name}`        | none                              | string | `{tag_name}`: the name of the tag to be deleted |

Usage：

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

Code：

| Code | description |
| ------ | ---- |
| 200    | sucess |
| other   | Please refer to the specific error message |
