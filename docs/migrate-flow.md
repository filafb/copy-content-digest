# Migration Script

## TL;DR

- Transform and save current content to `vtex.messages` with the following format:

```ts
interface Message {
  context: string // same as block id
  groupContext: 'CMS'
  srcLang: 'en-DV'
  srcMessage: 'text'
  targetMessage: string // content
}
```

- Create a new content file to be uploaded to `VBase`.

---

## Questions:

1. Any known issue wih the script?
2. What would be a good way to test it? Or what are the issues to pay attention when running it?
3. What is the final goal of the generated file? In the example, it generated only repeated messeges.
4. Should we transform the migration in an IO APP?
5. Where to save the generated file?
6. The `pages-graphql` is already saving messages in the format above. Can we assume the migration is only needed for content previously registered as `user content`?

---

## To investigate:

1. Bug?
- Create a page to `es` binding on `AmettlerOrigenQa` and changed the header;
- Linked both apps to allow coping binding;
- Copied a different page (not the new created) from `ca` to `es`;
- When getting the content with the migration script, the block content for the page created on step 1 is an empty array and we lose the previous edited content.

<details>

Content before copying the page:

```json
{
  "3nfrrM63PGWESjYfe37fYv": [
    {
      "blockContentMapId": "3nfrrM63PGWESjYfe37fYv",
      "condition": {
        "id": "be4fa4c4ff2661651aa33e656d1aa9d61e096392",
        "pageContext": { "id": "*", "type": "*" },
        "allMatches": true,
        "statements": [],
        "binding": "5d21161a-8e13-45ac-8767-e81fea5e56f0"
      },
      "content": {
        "text": "# This is the header for CA in the clone content test",
        "blockClass": "title"
      },
      "contentId": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      "contentLang": "ca-ES",
      "label": null,
      "messages": [],
      "messagesV2": [
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"]
        },
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"],
          "newKeyFormat": true
        }
      ],
      "origin": "ametllerorigenqa.store@1.x:rich-text#title-about-us"
    }
  ],
  "3kUmyFrNsrfxxACFyrTwnw": [
    {
      "blockContentMapId": "3kUmyFrNsrfxxACFyrTwnw",
      "condition": {
        "id": "be4fa4c4ff2661651aa33e656d1aa9d61e096392",
        "pageContext": { "id": "*", "type": "*" },
        "allMatches": true,
        "statements": [],
        "binding": "869a9be5-6ce6-4a3c-a47c-39d385391fdd"
      },
      "content": { "text": "# Header for ES", "blockClass": "title" },
      "contentId": "3kUmyFrNsrfxxACFyrTwnw~fcvodxpn3QCqLTn4zSKrxs",
      "contentLang": "es-ES",
      "label": null,
      "messages": [],
      "messagesV2": [
        {
          "context": "3kUmyFrNsrfxxACFyrTwnw~fcvodxpn3QCqLTn4zSKrxs",
          "data": "# Header for ES",
          "path": ["text"]
        },
        {
          "context": "3kUmyFrNsrfxxACFyrTwnw~fcvodxpn3QCqLTn4zSKrxs",
          "data": "# Header for ES",
          "path": ["text"],
          "newKeyFormat": true
        }
      ],
      "origin": "ametllerorigenqa.store@1.x:rich-text#title-about-us"
    }
  ],
  "wkQ6KuDMEKqn3gP7c6uQmP": [
    {
      "blockContentMapId": "wkQ6KuDMEKqn3gP7c6uQmP",
      "condition": {
        "id": "be4fa4c4ff2661651aa33e656d1aa9d61e096392",
        "pageContext": { "id": "*", "type": "*" },
        "allMatches": true,
        "statements": [],
        "binding": "5d21161a-8e13-45ac-8767-e81fea5e56f0"
      },
      "content": { "text": "Testing new stuff", "blockClass": "title" },
      "contentId": "wkQ6KuDMEKqn3gP7c6uQmP~gqWLcjAy3tNZbVEaS9RwZm",
      "contentLang": "ca-ES",
      "label": null,
      "messages": [],
      "messagesV2": [
        {
          "context": "wkQ6KuDMEKqn3gP7c6uQmP~gqWLcjAy3tNZbVEaS9RwZm",
          "data": "Testing new stuff",
          "path": ["text"]
        },
        {
          "context": "wkQ6KuDMEKqn3gP7c6uQmP~gqWLcjAy3tNZbVEaS9RwZm",
          "data": "Testing new stuff",
          "path": ["text"],
          "newKeyFormat": true
        }
      ],
      "origin": "ametllerorigenqa.store@1.x:rich-text#title-about-us"
    }
  ]
}
```

Content after copying the page:

```json
{
  "3nfrrM63PGWESjYfe37fYv": [
    {
      "blockContentMapId": "3nfrrM63PGWESjYfe37fYv",
      "condition": {
        "id": "be4fa4c4ff2661651aa33e656d1aa9d61e096392",
        "pageContext": { "id": "*", "type": "*" },
        "allMatches": true,
        "statements": [],
        "binding": "5d21161a-8e13-45ac-8767-e81fea5e56f0"
      },
      "content": {
        "text": "# This is the header for CA in the clone content test",
        "blockClass": "title"
      },
      "contentId": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      "contentLang": "ca-ES",
      "label": null,
      "messages": [],
      "messagesV2": [
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"]
        },
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"],
          "newKeyFormat": true
        }
      ],
      "origin": "ametllerorigenqa.store@1.x:rich-text#title-about-us"
    },
    {
      "blockContentMapId": "3nfrrM63PGWESjYfe37fYv",
      "condition": {
        "id": "be4fa4c4ff2661651aa33e656d1aa9d61e096392",
        "pageContext": { "id": "*", "type": "*" },
        "allMatches": true,
        "statements": [],
        "binding": "869a9be5-6ce6-4a3c-a47c-39d385391fdd"
      },
      "content": {
        "text": "# This is the header for CA in the clone content test",
        "blockClass": "title"
      },
      "contentId": "3nfrrM63PGWESjYfe37fYv~7aXJx9QUcVdAk1cK8YNZZN",
      "contentLang": "ca-ES",
      "label": null,
      "messages": [],
      "messagesV2": [
        {
          "context": "3nfrrM63PGWESjYfe37fYv~7aXJx9QUcVdAk1cK8YNZZN",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"]
        },
        {
          "context": "3nfrrM63PGWESjYfe37fYv~7aXJx9QUcVdAk1cK8YNZZN",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"],
          "newKeyFormat": true
        }
      ],
      "origin": "ametllerorigenqa.store@1.x:rich-text#title-about-us"
    }
  ],
  "3kUmyFrNsrfxxACFyrTwnw": [],
  "wkQ6KuDMEKqn3gP7c6uQmP": [
    {
      "blockContentMapId": "wkQ6KuDMEKqn3gP7c6uQmP",
      "condition": {
        "id": "be4fa4c4ff2661651aa33e656d1aa9d61e096392",
        "pageContext": { "id": "*", "type": "*" },
        "allMatches": true,
        "statements": [],
        "binding": "5d21161a-8e13-45ac-8767-e81fea5e56f0"
      },
      "content": { "text": "Testing new stuff", "blockClass": "title" },
      "contentId": "wkQ6KuDMEKqn3gP7c6uQmP~gqWLcjAy3tNZbVEaS9RwZm",
      "contentLang": "ca-ES",
      "label": null,
      "messages": [],
      "messagesV2": [
        {
          "context": "wkQ6KuDMEKqn3gP7c6uQmP~gqWLcjAy3tNZbVEaS9RwZm",
          "data": "Testing new stuff",
          "path": ["text"]
        },
        {
          "context": "wkQ6KuDMEKqn3gP7c6uQmP~gqWLcjAy3tNZbVEaS9RwZm",
          "data": "Testing new stuff",
          "path": ["text"],
          "newKeyFormat": true
        }
      ],
      "origin": "ametllerorigenqa.store@1.x:rich-text#title-about-us"
    },
    {
      "blockContentMapId": "wkQ6KuDMEKqn3gP7c6uQmP",
      "condition": {
        "id": "be4fa4c4ff2661651aa33e656d1aa9d61e096392",
        "pageContext": { "id": "*", "type": "*" },
        "allMatches": true,
        "statements": [],
        "binding": "869a9be5-6ce6-4a3c-a47c-39d385391fdd"
      },
      "content": { "text": "Testing new stuff", "blockClass": "title" },
      "contentId": "wkQ6KuDMEKqn3gP7c6uQmP~fj78ZvDozmYUMitn8Rcyhj",
      "contentLang": "ca-ES",
      "label": null,
      "messages": [],
      "messagesV2": [
        {
          "context": "wkQ6KuDMEKqn3gP7c6uQmP~fj78ZvDozmYUMitn8Rcyhj",
          "data": "Testing new stuff",
          "path": ["text"]
        },
        {
          "context": "wkQ6KuDMEKqn3gP7c6uQmP~fj78ZvDozmYUMitn8Rcyhj",
          "data": "Testing new stuff",
          "path": ["text"],
          "newKeyFormat": true
        }
      ],
      "origin": "ametllerorigenqa.store@1.x:rich-text#title-about-us"
    }
  ]
}
```

</details>

---

## To do

* [] Save file in the end of the flow into VBase S3. 

--- 

## Flow detail

### 1. Get all store content from `VBase S3` using `getContent` function;

<details>

Content example:
```JSON
{
  "3nfrrM63PGWESjYfe37fYv": [
    {
      "blockContentMapId": "3nfrrM63PGWESjYfe37fYv",
      "condition": {
        "id": "be4fa4c4ff2661651aa33e656d1aa9d61e096392",
        "pageContext": { "id": "*", "type": "*" },
        "allMatches": true,
        "statements": [],
        "binding": "5d21161a-8e13-45ac-8767-e81fea5e56f0"
      },
      "content": {
        "text": "# This is the header for CA in the clone content test",
        "blockClass": "title"
      },
      "contentId": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      "contentLang": "ca-ES",
      "label": null,
      "messages": [],
      "messagesV2": [
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"]
        },
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"],
          "newKeyFormat": true
        }
      ],
      "origin": "ametllerorigenqa.store@1.x:rich-text#title-about-us"
    }
  ]
}
```
</details>

<br>

### 2. Slice content 
_Usefull for testing the migration in part of the content_

### 3. Extract Messages from Content

Using the `slicedContent`, creates  an array called `allMessages`

For every block, transform the block content into message signature, e.g.:

```ts
  interface MessageInfo {
    contentId: string // same as the block id
    binding: string // binding id from content.condition
    messages: MessageV2[] // get from content.messagesV2
  }

  interface MessageV2 {
    context: string // the content id
    data: string // the content
    path: ['text']
  }
```

<details>

```js
[
  {
    contentId: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
    messages: [
      {
        context: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
        data: "# This is the header for CA in the clone content test",
        path: [
          "text",
        ],
      },
      {
        context: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
        data: "# This is the header for CA in the clone content test",
        path: [
          "text",
        ],
        newKeyFormat: true,
      },
    ],
    binding: "5d21161a-8e13-45ac-8767-e81fea5e56f0",
  },
]
```

</details>

<br>

### 4. Message by binding

Create a hash map `<binding: Message[]>` calle `messagesBinding`. If there is no content for a specifc binding, it's not included in the the hash map.

```ts
  interface Message {
    data: string // content
    path: ['text']
    contentId: string // same as the block id
  }
```

<details>

```js
{
  "5d21161a-8e13-45ac-8767-e81fea5e56f0": [
    {
      data: "# This is the header for CA in the clone content test",
      path: [
        "text",
      ],
      contentId: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
    },
    {
      data: "# This is the header for CA in the clone content test",
      path: [
        "text",
      ],
      contentId: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
    },
  ],
}
```

</details>

</br>

### 5. Get default language for bindings

Based on `messagesBinding` keys, creates a hash map `<bindingId, locale>` called `defaultLangsByBinding`. Get this information from `tenant` API.

<details>

```js
{
  "5d21161a-8e13-45ac-8767-e81fea5e56f0": "ca-ES",
}
```
</details>

</br>

### 6. Transform the messages to the new format

  - Add the default language to messages as `srcLang` field based on its binding - `messagesWithDefaultLang`;
  - Fetch messages from Vbase, after creating the file name using the same hash method on `vtex.messages` - `translations`;
  - Merge `messagesWithDefaultLang` with `translations`. Both are arrays and have to have the same size - result: 

  ```js
  const messagesWithTranslations = [
    {
      data: "# This is the header for CA in the clone content test",
      path: [
        "text",
      ],
      contentId: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      srcLang: "ca-ES",
      translations: {
        "ca-ES": "# This is the header for CA in the clone content test",
      },
      groupContext: "CMS",
    },
    {
      data: "# This is the header for CA in the clone content test",
      path: [
        "text",
      ],
      contentId: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      srcLang: "ca-ES",
      translations: {
        "ca-ES": "# This is the header for CA in the clone content test",
      },
      groupContext: "CMS",
    },
]
  ```

  - Build messages in the new format, with scrLang as `en-DV` and srcMessage as `text`, e.g.:

  ```js
const messagesToSave = [
    {
      srcLang: "en-DV",
      srcMessage: "text",
      targetMessage: "# This is the header for CA in the clone content test",
      groupContext: "CMS",
      context: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      to: "ca-ES",
    },
    {
      srcLang: "en-DV",
      srcMessage: "text",
      targetMessage: "# This is the header for CA in the clone content test",
      groupContext: "CMS",
      context: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      to: "ca-ES",
    },
]
  ```

- Transform `messagesToSave` in a hash map as `<targetLanguage, Message[]>`

```ts
interface Message {
  context: string // same as block id
  groupContext: 'CMS'
  srcLang: 'en-DV'
  srcMessage: 'text'
  targetMessage: string // content
}
```

### 7. Save messages into `vtex.messages`

- For every locale, creates an object with `to` and all the `messages`

```js
[
  {
    messages: [
      {
        srcLang: "en-DV",
        srcMessage: "text",
        targetMessage: "# This is the header for CA in the clone content test",
        groupContext: "CMS",
        context: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      },
      {
        srcLang: "en-DV",
        srcMessage: "text",
        targetMessage: "# This is the header for CA in the clone content test",
        groupContext: "CMS",
        context: "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      },
    ],
    to: "ca-ES",
  },
]
```

### 8. Generate a new content file

<details>

```json
{
  "3nfrrM63PGWESjYfe37fYv": [
    {
      "blockContentMapId": "3nfrrM63PGWESjYfe37fYv",
      "condition": {
        "id": "be4fa4c4ff2661651aa33e656d1aa9d61e096392",
        "pageContext": { "id": "*", "type": "*" },
        "allMatches": true,
        "statements": [],
        "binding": "5d21161a-8e13-45ac-8767-e81fea5e56f0"
      },
      "content": {
        "text": "# This is the header for CA in the clone content test",
        "blockClass": "title"
      },
      "contentId": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
      "contentLang": "ca-ES",
      "label": null,
      "messages": [],
      "messagesV2": [
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"]
        },
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"],
          "newKeyFormat": true
        },
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"],
          "newKeyFormat": true
        },
        {
          "context": "3nfrrM63PGWESjYfe37fYv~vTa2hmuK9UomdF68bbMV92",
          "data": "# This is the header for CA in the clone content test",
          "path": ["text"],
          "newKeyFormat": true
        }
      ],
      "origin": "ametllerorigenqa.store@1.x:rich-text#title-about-us"
    }
  ]
}
```
</details>

---
## To remember

- When calling `getContent` in an account without user content, it gets a `404` error.