# Edit Method

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

## Move subscription 

Endpoint：`subscription/edit`

Method：`POST`

Data：

| parameter | required | sample value            | default value | type   | description                                         |
| ------ | ---- | --------------- | ------ | ------ | -------------------------------------------- |
| `ac`     | yes   | `edit`            | none     | string | Operation type                                      |
| `s`      | yes   | `feed/{feed_url}` | none     | string | `{feed_url}`: Links to RSS feeds to be moved                |
| `r`      | yes   | `user/-/label/{old_folder}` | none     | string | `{old_folder}`: The name of the folder where the subscription feed is currently located, using the `user/-/label/` prefix |
| `a`      | yes   | `user/-/label/{new_folder}` | none     | string | `{new_folder}`: Subscription source target folder name, using the `user/-/label/` prefix |

Usage：

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

Response：

| Code | description       |
| ------ | ---------- |
| 200    | sucess       |
| other    |  Please refer to the specific error message  |


## Rename subscription

Endpoint：`subscription/edit`

Method：`POST`

Data：

| parameter | required | sample value           | default value | type   | description                               |
| ------ | ---- | -------------- | ------ | ------ | ---------------------------------- |
| `ac`     | yes   | `edit`          | none     | string | Operation Type                             |
| `s`      | yes   | `feed/{feed_url}` | none     | string | `{feed_url}`: Links to RSS feeds to be renamed |
| `t`      | yes   | `{new_feed_title}`    | none     | string | `{new_feed_title}`: New subscription feed name           |

Usage：

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

Response：

| Code | description       |
| ------ | ---------- |
| 200    | sucess       |
| other    | Please refer to the specific error message |


## Edit subscription tag/create folder (if it doesn't exist)

Endpoint：`edit-tag`

Method：`POST`

Data：

| parameter | required | sample value                            | default value | type   | description                                                            |
| ------ | ---- | -------------------------------- | ------ | ------ | --------------------------------------------------------------- |
| `ac`     | yes   | `edit`                             | none     | string | Operation Type                                                        |
| `a`      | yes   | `user/-/label/{folder}`             | none     | string | user label/folder path, `{folder}` for label/folder name, use `user/-/label/` prefix  |
| `s`      | yes   | `feed/{feed_url}`                 | none     | string | `{feed_url}`: the link to the RSS feed to be added to the tag, prefixed with `feed/`.**A feed must be provided or the folder cannot be created** |

Usage：

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


Response：

| Code | description                                      |
| ------ | ----------------------------------------- |
| 200    | sucess                                      |
| other    | Please refer to the specific error message |


## Delete Folder

Endpoint：`disable-tag`

Method：`POST`

Data：

| parameter | required | sample value                            | default value | type   | description                                                          |
| ------ | ---- | -------------------------------- | ------ | ------ | ------------------------------------------------------------- |
| `ac`     | yes   | `disable-tags`                   | none     | string | Operation Type                                                       |
| `s`      | yes   | `user/-/label/{folder}`             | none     | string | user label/folder path, `{folder}` is the folder name, use `user/-/label/` prefix |
| `t`      | yes   | `{folder}`                        | none     | string |  Name of the folder to be deleted                                          |

!!! warning
    This will only delete the folder itself, not the subscription feeds within it.

Usage：

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

Response：

| Code | description                               |
| ------ | ----------------------------------- |
| 200    | sucess                               |
| other    | Please refer to the specific error message |


## Cancellation of specified article sharing

!!!info
    For self-deploying RSS readers, shared items may be useless and unsupported, so not all API-compatible implementations will be able to call this API

Endpoint：`edit-tag`

Method：`POST`

参数：

| parameter | required | sample value                               | default value | type   | description                                         |
| ------ | ---- | ------------------------------------ | ------ | ------ | -------------------------------------------- |
| `r`      | yes   | `user/-/state/com.google/broadcast` | none     | string | Cancelling a shared operation                                   |
| `i`      | yes   | `{entry_id}`                          | none     | string | The `{entry_id}` corresponding to the article can be found in the `<entry>` tag |
| `async`  | 否   | `true`                               | `true`  | boolean   | Whether to execute asynchronously                                 |

Usage：

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

Response：

| Code | description                 |
| ------ | -------------------- |
| 200    | sucess                 |
| other    | Please refer to the specific error message |



!!! note
    To rename a tag/folder, you should delete the old tag/folder and then apply the new tag/folder to all the posts that originally had the old tag/folder

## Changing Label Sharing Settings

!!! note
    Google Reader allows sharing of tags, which means that a publicly accessible HTML page and RSS feed can be created for each tag.


Endpoint：`tag/edit`

Method：`POST`

parmas：

| parameter   | required | sample value     | default value | type   | description              |
| -------- | ---- | ---------- | ------ | ------ | ----------------- |
| `client` | yes   | `settings` | none     | string | fixed value `settings` |

Data：

| parameter | required | sample value               | default value | type   | description                               |
| ------ | ---- | -------------------- | ------ | ------ | ---------------------------------- |
| `s`      | yes   | `user/-/label/{tag}` | none     | string | `{tag}`: Corresponding tags                  |
| `t`      | yes   | `{tag}`              | none     | string | `{tag}`: Corresponding tags                  |
| `pub`    | yes   | `true` / `false`     | `true`  | boolean   | Publicly released or not, `true` for public, `false` for private |

Usage：

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
    The address of the shared tag's HTML page is: `~/reader/shared/user/{userId}/label/{tag}`, and its RSS feed is: `~/reader/public/atom/user/{userId}/label/{tag}`.
    And its RSS feed is at `~/reader/public/atom/user/{userId}/label/{tag}`.