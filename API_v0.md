# WOVN API v0

API for translating website.

## Paths

### GET https://api.wovn.io/v0/values

Returns translated texts and replacing image URLs of requested page. These are used by WOVN backend libraries.

#### Parameters

Name  | Located in | Description                        | Required | Schema
----- | ---------- | ---------------------------------- | -------- | ------
token | query      | User token of your WOVN.io account | yes      | string
url   | query      | URL of page                        | yes      | string

#### Sample responses

GET https://api.wovn.io/v0/values (no token and url)

```json
Status Code: 400
Content-Type: application/json
```

```javascript
{
  "code": 400,
  "message": "url and token parameters are required."
}
```

GET https://api.wovn.io/v0/values?token=2Wle3 (no url)

```json
Status Code: 400
Content-Type: application/json
```

```javascript
{
  "code": 400,
  "message": "url parameter is required."
}

```

GET https://api.wovn.io/v0/values?url=wovn.io%2Fcontact (no token)

```json
Status Code: 400
Content-Type: application/json
```

```javascript
{
  "code": 400,
  "message": "token parameter is required."
}

```

GET https://api.wovn.io/v0/values?token=2Wle3&url=wovn.io%2Fcontact

```json
Status Code: 200
Content-Type: application/json
```

```javascript
{
  // Translated texts of requested page.
  "text_vals": {
    "Orders": {                                                      // Original text.
      "ja": [                                                        // Translation language. When this is empty, this text is not translated and the original is used.
        {
          "xpath": "/html/body/div/div/ul/li[4]/ul/li[5]/a/text()",  // XPath of original text in original page.
          "data": "オーダー"                                          // Translated text.
        }
      ],
      ...
    },
    ...
  },
  
  // Replacing images of requested page.
  "img_vals": {
    "https://wovn.io/assets/header_logo.png": {                     // Original image URL.
      "": [                                                         // Translation language. When this is empty, this image is not replaced and the original is used. 
        {
          "xpath": "/html/body/div/a/img",                          // XPath of original image in original page.
          "data": "https://s3-us-west-1.amazonaws.com/st.wovn.io/"  // Replacing image URL.
        }
      ]
    },
    ...
  },

  // This property is not used and is always empty now. 
  "layout_vals": {
  }
}
```

### POST https://api.wovn.io/v0/page/values

Searches translated texts from requested domain.

#### Parameters

Name       | Located in | Description                                               | Required | Schema
---------- | ---------- | --------------------------------------------------------- | -------- | ------
values     | body       | Translated texts to search. Specifies text as JSON array. | yes      | string
domain     | body       | Domain to search texts                                    | yes      | string
target     | body       | Translation lanuage                                       | yes      | string
user_token | body       | User token of your WOVN.io account                        | yes      | string

#### Sample responses

POST https://api.wovn.io/v0/values

* values: ["hello", "abcde"]
* domain: example.com
* target: ja
* user_token: abcde

```json
Status Code: 200
Content-Type: application/json
```

```javascript
{
  "result": [
    {
      "src": "hello",      // Original text.
      "tgt": "こんにちは"  // Translated text.
    },
    {
      "src": "abcde",
      "tgt": " "           // When there is not translated text in requested domain, a space is returned.
    }
  ]
}
```

### (ALPHA) POST https://api.wovn.io/v0/translation

Translate HTML content of a page through the translation API

#### Parameters

Name       | Located in | Description                                               | Required | Schema
---------- | ---------- | --------------------------------------------------------- | -------- | ------
url        | body       | URL of the page as created in the WOVN.io website         | yes      | string
token      | body       | Domain token of the project (or User token)               | yes      | string
lang_code  | body       | Language to translate the page into                       | yes      | string
url_pattern| body       | URL pattern used on the backend (path, query, subdomain)  | yes      | string
body       | body       | HTML content of the page MUST BE URL ENCODED              | yes      | string


#### Sample responses

POST https://api.wovn.io/v0/translation

* url: http://wovn.io
* token: 123456
* lang_code: ja
* url_pattern: path
* body: ```%3Chtml%3E%3Chead%3E%3Ctitle%3ETest+Title%3C%2Ftitle%3E%3C%2Fhead%3E%3Cbody%3E%3Cp%3ETest+text%3C%2Fp%3E%3C%2Fbody%3E%3C%2Fhtml%3E```
(body is url encoded)

```json
Status Code: 200
Content-Type: application/json
```

```javascript
{
  "body": "<html><head><script src=\"//j.wovn.io/1\" async=\"true\" data-wovnio=\"key=123456&amp;backend=true&amp;currentLang=ja&amp;defaultLang=en&amp;urlPattern=path&amp;langCodeAliases=[]&amp;version=api\"></script><title>テストタイトル</title></head><body><p>テストテキスト</p></body></html>"
}
```
