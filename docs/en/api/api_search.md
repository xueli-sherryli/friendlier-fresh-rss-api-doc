# Search Methods

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

 !!! warning
    Note the change in the API endpoint, `directory/search` does not contain `/api/0/` in the API request path.

## Search GReader Categories

Endpoint: `directory/search`

Method: `GET`

params:

| parameter | required | sample value           | default value | type   | description                     |
| ------ | ---- | ------------- | ------ | ------ | -------------------------- |
| q      | yes   | {search_term} | none     | string | Search keyword               |

Usage：

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

Return result: Search result page in HTML format.


## Search items ids

Endpoint: `search/items/ids`

Method: `GET`

params:

| parameter | required | sample value           | default value | type   | description                                   |
| ------ | ---- | ------------- | ------ | ------ | -------------------------------------- |
| q      | yes   | {search_term} | none     | string | Search keyword                            |
| output | recommend | xml/json       | xml    | string | Output format (`json` is recommended for easier processing)  |

Usage：

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
    
Returns: an object containing a list of item IDs that match the search term.


## Search items contents

Endpoint: `stream/items/contents`

Method: `POST`

params:

| parameter | required | sample value        | default value | type   | description                                                         |
| ------ | ---- | --------- | ------ | ------ | ------------------------------------------------------------ |
| output | recommend | atom/json | atom   | string | Output format. Note: Even if `atom` is specified, this interface returns JSON format when not explicitly specified. |

Data:

| parameter | required | sample value                               | default value | type   | description                                                       |
| ------ | ---- | --------------------------------- | ------ | ------ | ---------------------------------------------------------- |
| i      | yes   | {item_id_1}&i={item_id_2}&...  | 无     | string | Multiple item IDs, use `&i=` to concatenate                                 |

Usage：

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
Return result: the content of the item with the specified ID.  Note: Even if `atom` output is specified, the interface returns JSON format if the output format is not explicitly specified.