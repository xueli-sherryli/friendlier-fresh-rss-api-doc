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

## Get subscription list

Endpoint：`subscription/list`

Method：`GET`

params：

| parameter | required | sample value       |default value | type   | description           |
| ------ | ---- | ------------ | ------ | ------ | -------------- |
| output | yes   | `json`/`xml` | `xml`  | string | Select the format of the output |

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/list?output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/subscription/list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

When `output=xml`, the return example is as follows

```xml
<object> 
<string name="id">feed/http://feeds.feedburner.com/NickBradbury</string>  
<string name="title">Nick Bradbury</string>  
<list name="categories"> 
<object> 
<string name="id">user/[USER_ID]/label/Bradbury Software</string>  
<string name="label">Bradbury Software</string>  
</object> 
</list> 
<string name="sortid">C430EC8F</string>  
<number name="firstitemmsec">1203460831522</number>  
</object> 
```

When `output=json`, the return example is as follows

```json
{
	"subscriptions": [{
		"id": "feed/39",
		"title": "标题测试1",
		"categories": [{
			"id": "user/-/label/Uncategorized",
			"label": "Uncategorized"
		}],
		"url": "https://example.com",
		"htmlUrl": "https://example-html.com",
		"iconUrl": "http://example/public.php?op=feed_icon&id=39.ico"
	}, {
		"id": "feed/31",
		"title": "标题测试2",
		"categories": [{
			"id": "user/-/label/分类测试1",
			"label": "分类测试1"
		}],
		"url": "https://example.com",
		"htmlUrl": "https://example-html.com",
		"iconUrl": "http://example/public.php?op=feed_icon&id=31.ico"
	}, {
		"id": "feed/30",
		"title": "标题测试3",
		"categories": [{
			"id": "user/-/label/分类测试1",
			"label": "分类测试1"
		}],
		"url": "https://example.com",
		"htmlUrl": "https://example-html.com",
		"iconUrl": "http://example/public.php?op=feed_icon&id=30.ico"
	}]
}
```
!!! info
    The name of the folder is stored as <string name=‘label’> in the <list name=‘categories’> node. Feeds in the root folder have an empty <list name=‘categories’/> node. The firstitemmsec attribute is the timestamp of the oldest (first) item in that user's subscription.
    
    A simpler way is to call `subscriptions/export` as a GET method to get an export of the OPML file, but this does not include any GReader data (such as firstitemmsec).
    
    **Note:** The `/` in the request parameter needs to be URL encoded, e.g. `/` is encoded as `%2F`.
    
    **Note:** The operation will fail unless the feed parameter (s parameter) is passed. (It is not possible to create an empty folder, you must create a folder with a subscription feed)


## Get the total unread count of the reader

Endpoint：`unread-count`

Method：`GET`

params：

| parameter | required | sample value  |default value | type   | description                                                         |
| ------ | ---- | ------ | ------ | ------ | ------------------------------------------------------------ |
| output | yes   | `xml`/`json` | `xml`  | string | Select the format of the output                                            |

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/unread-count?output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/unread-count'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

If there are unread entries, return the `xml` sample as follows

```xml
<object> 
    <number name="max">1000</number> 
    <list name="unreadcounts"> 
        <object> 
            <string name="id">user/-/state/com.google/reading-list</string> 
            <number name="count">8</number> 
            <number name="newestItemTimestampUsec">1232641608774062</number> 
        </object> 
        <object> 
            <string name="id">feed/http://rss.cnn.com/rss/cnn_topstories.rss</string> 
            <number name="count">4</number> 
            <number name="newestItemTimestampUsec">1232641203555343</number> 
        </object> 
        <object> 
            <string name="id">user/-/label/FolderName</string> 
            <number name="count">8</number> 
            <number name="newestItemTimestampUsec">1232641608774062</number> 
        </object> 
        <object> 
            <string name="id">feed/http://www.engadget.com/rss.xml</string> 
            <number name="count">4</number> 
            <number name="newestItemTimestampUsec">1232641608774062</number> 
        </object> 
</list> 
</object> 
```
Returns a `json` example as follows

```json
{
    "max": 8
    "unreadcounts": [
        {
            "id": "user/-/state/com.google/reading-list",
            "count": 8,
            "newestItemTimestampUsec": "1232641608774062"
        },
        {
            "id": "feed/http://rss.cnn.com/rss/cnn_topstories.rss",
            "count": 4,
            "newestItemTimestampUsec": "1232641203555343"
        },
                {
            "id": "user/-/label/FolderName",
            "count": 8,
            "newestItemTimestampUsec": "1232641608774062"
        },
        {
            "id": "feed/http://www.engadget.com/rss.xml",
            "count": 4,
            "newestItemTimestampUsec": "1232641608774062"
        }
        ...
    ]
}
```

If there are no unread entries, return the `xml` sample as follows

```xml
<object> 
    <number name="max">1000</number> 
    <list name="unreadcounts"/> 
</object> 
```

Returns a `json` example as follows

```json	
{
  "max": 1000,
  "unreadcounts": []
}
```



## Get all entries

Endpoint：`stream/contents/user/-/state/com.google/reading-list`

Method：`GET`

params：

| parameter | required | sample value          |default value | type   | description                                                         |
| ------ | ---- | ---------------------------- | ------ | ------ | ------------------------------------------------------------ |
| n      | no   | 20                           | 20     | number | Maximum number of requests, up to 1000                                       |
| r      | no   | o / n                        | o      | string | Sort by. o means sort by posting time (old->new), n means sort by updating time (new->old). |
| t      | no   | 1678886400                   | none     | number | Get articles that are *more recent* than the specified timestamp (Unix timestamp, milliseconds)               |
| ot     | no   | 1678886400                   | none     | number |Get articles *older* than the specified timestamp (Unix timestamp, milliseconds)             |
| xt     | no   | user/-/state/com.google/read | none     | string | Exclude tab, example for excluding read content                                 |
| c      | no   | continuation string          | none     | string | Continued reading string for paging to load more entries                           |
| output | yes   | xml/json                     | json   | string | Sample output, defaults to `json`                                         |

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/stream/contents/user/-/state/com.google/reading-list?n=20&r=o&ot=1678886400&xt=user/-/state/com.google/read&output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/stream/contents/user/-/state/com.google/reading-list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'n': 20,
        'r': 'o',
        'ot': 1678886400,
        'xt': 'user/-/state/com.google/read',
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

Response：



## Get user info

Endpoint：`user-info`

Method：`GET`

Parmas：None

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/user-info?T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/user-info'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params ={
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

Response:

```json
{
    "userId":"admin",
    "userName":"admin",
    "userProfileId":"admin",
    "userEmail":""
}
```

## Get user's friend information

Endpoint：`friend/list`

Method：`GET`

Parmas：None

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/friend/list&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/friend/list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params ={
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```


## Getting information about reader preferences

Endpoint：`preference/list`

Method：`GET`

Parmas：None

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/preference/list&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/preference/list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params ={
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

## Get starred articles

Endpoint:`preference/list/stream/contents/user/-/state/com.google/starred`

Method：`GET`

Parmas:

| parameter | required | sample value   |default value | type   | description                          |
| ------ | ---- | -------- | ------ | ------ | ----------------------------- |
| n      | yes   | 20       | none     | number | Get the number of starred articles, the maximum is 1000. |
| output | no   | json/xml | json   | string | Sample output, defaults to `json`     |

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/stream/contents/user/-/state/com.google/starred?n=20&output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/stream/contents/user/-/state/com.google/starred'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'n': 20,
        'output': 'json',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

Response

```JSON
{
  "id": "user/-/state/com.google/reading-list",
  "updated": 1738469151,
  "items": [
    {
      "id": "tag:google.com,2005:reader/item/000000000001035c",
      "crawlTimeMsec": "1738411626000",
      "timestampUsec": "1738411626000000",
      "published": 1738411626,
      "title": "...",
      "canonical": [
        {
          "href": "...""
        }
      ],
      "alternate": [
        {
          "href": "...""
        }
      ],
      "categories": [
        "user/-/state/com.google/read",
        "user/-/state/com.google/starred"
      ],
      "origin": {
        "streamId": "feed/31",
        "title": "..."",
        "htmlUrl": "...""
      },
      "summary": {
        "content": "...""
      },
      "author": ""
    }
  ],
  "continuation": "1"
}
```



## Prase feed url

Endpoint：`/atom/feed/{feed_url}`

Method：`GET`

Parmas:

| parameter   | required | sample value                                   |default value | type   | description               |
| -------- | ---- | ---------------------------------------- | ------ | ------ | ------------------ |
| feed_url | yes   | http://feeds.feedburner.com/NickBradbury | none     | string | RSS links to be prased    |
| n        | no   | 40                                       | 20     | number | Number of loaded article entries |
| xt       | no   | user/-/state/com.google/read             | none     | string | Excluded labels         |

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/atom/feed/http://feeds.feedburner.com/NickBradbury?n=40&xt=user/-/state/com.google/read&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/atom/feed/http://feeds.feedburner.com/NickBradbury'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'n': 40,
        'xt': 'user/-/state/com.google/read',
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

Response:

```xml
<entry>
  <id>tag:google.com,2005:reader/item/30fd6c8e007ebcbf</id>
  <published>2009-01-03T18:37:04Z</published>
  <updated>2009-01-03T18:37:04Z</updated>
  <title>[Article summary]</title>
  <content type="html">[Article content]</content>
  <author>
    <name>Nick Bradbury</name>
  </author>
  <link rel="alternate" href="http://example.com/article" />
  <category scheme="http://www.google.com/reader/atom" term="tag:google.com,2005:reader/feed/http://feeds.feedburner.com/NickBradbury" />
  <gr:crawl-timestamp-msec>1233716224000</gr:crawl-timestamp-msec>  <!-- Unix timestamp -->
  <gr:is-read-state-locked>true</gr:is-read-state-locked> <!-- If an entry is locked as unread -->
</entry>
```
!!! note
     **`gr:crawl-timestamp-msec`** Indicates the Unix timestamp (in milliseconds) at which Google Reader received the article.
    

     **`gr:is-read-state-locked=‘true’`**: This attribute is included if the reading state of the entry is locked (‘remain unread’).
    
     **`<id>`** The value of the entry is always the ID in Google Reader, replacing the original GUID/ID in the subscription feed. The original GUID/ID can still be retrieved from the ID node, e.g..
     
     `<id gr:original-id=‘http://nick.typepad.com/blog/2009/01/’>tag:google.com,2005:reader/item/30fd6c8e007ebcbf</id>`
    
    **`Categories`** is used to reflect not only the category assigned to the entry in the actual feed, but also the read/tagged status in GReader. 
    
    * If an article has been read, its **`Categories`** will look like this:
        * ```<category term=‘user/[userid]/state/com.google/read’ scheme=‘http://www.google.com/reader/’ label=‘read’ />```
    * If an article has been starred, its **``Categories`` ** will look like this:
        * ```<category term=‘user/[userid]/state/com.google/starred’ scheme=‘http://www.google.com/reader/’ label=‘starred’ /> ``` * If an article is in a folder, it will look like this.
    * If an article is in a folder, its **`Categories`** will look like this
        * ``` <category term=‘user/[userid]/label/[folderName]’ scheme=‘http://www.google.com/reader/’ label=‘[folderName]’/> ```

An important part of Google Reader's (GReader's) access to the feed is the **continuation** feature, which provides a way to page through entries in the feed. For example, if you request the latest 20 entries in the feed, the root element might contain a **`gr:continuation`** child element, for example:

```xml
<feed xmlns:gr="http://www.google.com/schemas/reader/atom/" xmlns="http://www.w3.org/2005/Atom"> 
    <generator uri="http://www.google.com/reader">Google Reader</generator> 
    <id>tag:google.com,2005:reader/feed/http://feeds.feedburner.com/NickBradburyClippings</id> 
    <title>Nick Bradbury&amp;#39;s Shared Items on NewsGator Online</title> 
    <updated>2009-01-15T15:17:16Z</updated> 
<gr:continuation>CP__hN2fq5gC</gr:continuation>  
```

Simply append the gr:continuation value to subsequent requests to get the next 20 entries in the subscription feed, for example:
GET http://www.google.com/reader/atom/feed/http://feeds.feedburner.com/NickBradbury&c=CP__hN2fq5gC 
This makes it possible to access the history of the subscription feed, which may be useful immediately after subscribing to the feed or the first time a user chooses to enable GReader synchronisation.

## Get user's shared entires

!!!info
    For self-hosting RSS readers, shared items may be useless and unsupported, so not all API-compatible implementations will be able to call this API

Endpoint：`/reader/atom/user/-/state/com.google/broadcast&n={count}`

Method：`GET`

Parmas：

| parameter  | required | sample value |default value | type   | description                     |
| ------- | ---- | ------ | ------ | ------ | ------------------------ |
| {count} | yes   | 20     | none     | number | To get the number of articles shared by users |

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/reader/atom/user/-/state/com.google/broadcast?n=20&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/reader/atom/user/-/state/com.google/broadcast'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'n': 20,
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
    ```

!!!note

    - Note that the entry ID must be the ID of the entry in the GReader, so entries from non-GReader sources need to be shared as if they were arbitrary URLs, for example:
    - POST item/edit?share=true&title={title}&url={url}&snippet={snippet}&srcTitle=‘{srcTitle}’&srcUrl={srcUr}.
    - When retrieving the user's shared project again, you will see that GReader has assigned an entry ID to this project. sourceTitle and srcUrl parameters are the name and link of the project's source page/source, respectively. You can also add the annotation parameter. The annotated shared project entry (‘Shared with notes’) will contain something like this

## Get a list of tags (folders)

Endpoint：`tag/list`

Method：`GET`

Parmas：

| parameter | required | sample value   |default value | type   | description       |
| ------ | ---- | -------- | ------ | ------ | ---------- |
| output | yes   | xml/json | none     | string | Sample output, defaults to `json` |

usage：

=== "Curl"

    ```bash
    curl -s -H 'Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8' -H 'Cookie: SID=YOUR_SID' \
        'https://freshrss.example.net/api/greader.php/reader/api/0/tag/list?output=json&client=myclient&T=YOUR_T_TOKEN'
    ```

=== "Python"

    ```python
    import requests

    url = 'https://freshrss.example.net/api/greader.php/reader/api/0/tag/list'
    header = {
        "Authorization: GoogleLogin auth=alice/8e6845e089457af25303abc6f53356eb60bdb5f8"
    }
    params = {
        'output': "json",
        'client': 'myclient',
        'T': 'YOUR_T_TOKEN'
    }
    cookies = {'SID': 'YOUR_SID'}
    try:
        response = requests.get(url, headers=header, params=params, cookies=cookies)
        response.raise_for_status()
        print(response.text)

    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")

If `output=json`, the Response is as follows:

```json
{
	"tags": [{
		"id": "user/-/state/com.google/starred"
	}, {
		"id": "user/-/label/A",
		"type": "folder"
	}, {
		"id": "user/-/label/B",
		"type": "folder"
	}, {
		"id": "user/-/label/C",
		"type": "folder"
	}, {
		"id": "user/-/label/D",
		"type": "folder"
	}, {
		"id": "user/-/label/E",
		"type": "folder"
	}, {
		"id": "user/-/label/F",
		"type": "tag"
	}, {
		"id": "user/-/label/G",
		"type": "folder"
	}, {
		"id": "user/-/label/H",
		"type": "tag"
	}, {
		"id": "user/-/label/I",
		"type": "folder"
	}, {
		"id": "user/-/label/J",
		"type": "folder"
	}, {
		"id": "user/-/label/K",
		"type": "folder"
	}, {
		"id": "user/-/label/L",
		"type": "tag"
	}, {
		"id": "user/-/label/Uncategorized",
		"type": "folder"
	}, {
		"id": "user/-/label/测试",
		"type": "tag"
	}]
}
```

!!! note
    Since Google Reader treats folders as labels, this list will contain folder names. The client needs to parse out the actual folder name labels themselves.

  