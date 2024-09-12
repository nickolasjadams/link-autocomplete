# Link Autocomplete

An upgraded version of base inline link tool with your server's search.
A fork of the codex plugin. I tried multiple times (<a href="https://github.com/editor-js/link-autocomplete/pull/9">PR1</a>, <a href="https://github.com/editor-js/link-autocomplete/pull/11">PR2</a>) to work with the maintainer 
to create PRs that they would merge, but they wouldn't provide helpful feedback as to why they weren't suitable to merge other than that the first PR was too large. The second PR was a smaller PR fixing less problems at once with a list of test instructions. This PR was never responded to. 

This version fixes 
- Fixes issue #8: Impossible to disable server search
  - You may disable server calls by removing the endpoint from the config.
- Fixes unreported issue: Url Validation is broken.
- Fixes issue #6: Support mailto: URL scheme
- Added support for 3 URI schemes
  - view-source
  - mailto
  - tel
- Added support for Url fragments like this &lt;a href="#lower_in_the_document"&gt; (works well in conjunction with https://github.com/Aleksst95/header-with-anchor)
- Add a checkbox to support opening links in new tabs
- Fixed warnings from the linter.
- Adds type='button' to inline toolbar to prevent form submissions when editor.js is placed within form tags.


![](example/assets/example.png)

## Installation

### Install via NPM

Get the package

```shell
npm i --save-dev @editorjs/link-autocomplete
```

```shell
yarn add -D @editorjs/link-autocomplete
```

### Load from CDN

You can use package from jsDelivr CDN.

```html
<script src="https://cdn.jsdelivr.net/npm/@editorjs/link-autocomplete"></script>
```

## Usage

Add a new Tool to the `tools` property of the Editor.js initial config.

```javascript
var editor = EditorJS({
  ...
 
  /**
   * Tools list
   */
  tools: {
    link: {
      class: LinkAutocomplete,
      config: {
        endpoint: 'http://localhost:3000/',
        queryParam: 'search'
      }
    }
  },
  
  ...
});
```

## Config Params

Search requests will be sent to the server by `GET` requests with a search string as a query param. 

List of server connection params which may be configured.

`endpoint` — URL of the server's endpoint for getting suggestions.

`queryParam` — param name to be sent with the search string.

If there is no `endpoint` then tool will work only for pasted links.

## Server response data format

For endpoint requests server **should** answer with a JSON containing following properties:

- `success` (`boolean`) — state of processing: `true` or `false`  
- `items` (`{name: string, href: string, description?: string}`) — an array of found items. Each item *must* contain `name` and `href` params. The `description`
param is optional. You can also return any other fields which will be stored in a link dataset.

Content-Type: `application/json`.

```json
{
  "success": true,
  "items": [
    {
      "href": "https://codex.so/editor",
      "name": "The first item",
      "description": ""
    },
    {
      "href": "https://codex.so/media",
      "name": "The second item",
      "description": ""
    }
  ]
}
```

## Output data

Marked text will be wrapped with a `a` tag as a usual link.

Additional data will be store in element's dataset: `data-name`, `data-description` and other custom fields.

```json
{
    "type" : "text",
    "data" : {
        "text" : "Create a directory for your module, enter it and run <a href=\"https://codex.so/\" data-name=\"CodeX Site\">npm init</a> command."
    }
}
```

## Shortcut

By default, shortcut `CMD (CTRL) + K` is used for pasting links as usual.

## I18n

There are a few phrases to be translated. 

UI items:

- `Paste or search` — placeholder for an input field if server endpoint passed.
- `Paste a link` — placeholder for the same field if server endpoint is missing.
- `Open in new tab` - label for checkbox field that changes link target.
- `Opens in a new tab` - Descriptive text for existing links that open in a new tab.
- `Opens in same tab` - Descriptive text for existing links that open in the same tab.

Error messages:

- `Cannot process search request because of` — message before error's text in notification for a bad server response.
- `Server responded with invalid data` — bad server's response
- `Link URL is invalid` — pasted link url is bad 

```
i18n: {
  messages: {
    tools: {
      LinkAutocomplete: {
        'Paste or search': '...',
        'Paste a link': '...',
        'Open in new tab': '...',
        'Opens in a new tab': '...',
        'Opens in same tab': '...',
        'Cannot process search request because of': '...',
        'Server responded with invalid data': '...',
        'Link URL is invalid': '...'
      }
    }
  }
},
```