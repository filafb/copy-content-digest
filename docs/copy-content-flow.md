# Copy Content

## Questions:
1. Muji has `vtex.pages-graphql@1.x`. What is the potential breaking changes to `@2.x`?
2. Mutation `saveRoute` doesn't return the `conflicts`. Expected behavior?

<details>

---

```gql
mutation saveRoute($args: NewRouteInput){
  saveRoute(route: $args) {
    conflicts {
      binding
      blockId
      path
      context
      declarer
    }
  }
}
```

```json
{
    "args": {
    "auth": false,
    "bindingId": "4e8718d4-3201-44ae-872f-9e1c33ef0a73", // also: “81f40404-5e9e-4d2b-8493-172394a0289c”
    "blockId": "muji.store-theme@2.x:store.custom#handover",
    "context": null,
    "declarer": null,
    "domain": "store",
    "interfaceId": "vtex.store@2.x:store.custom",
    "metaTags": {
      "description": "",
      "keywords": []
    },
    "pages": [],
    "path": "/new-test-find-resolver3",
    "routeId": "vtex.store@2.x:store.custom#new-test-find-resolver3",
    "title": "new test find resolver",
    "dataSource": "vtex.rewriter"
  }
}
```

</details>

3. Should we make a PR pointing to the current WIP branches or keep working on them? Can the CMS team rebase the branches?
4. `UX`: Should we show the copy button only for custom pages?

---
## To investigate:

1. Bug
- `vtex.pages-graphql` returns an error when pages doesn't have `content` and the page get stuck in `saving...` status. [code](https://github.com/vtex/pages-graphql/blob/f018e7cfd1b8bf8558aa5a724f26c9a415b0186f/node/v2/resolvers/copyBindingContent.ts#L48-L50)

2. Bug / UX
- Copy an existing page from binding `y` to binding `x`;
- On `pages`, change the slug for that page on binding `x`;
- When openning the modal to copy content, the slug that appers is the one for page `x`;
- If copy content to page that doesn't have that page, it will have the slug of the page `x`;
- If copy page to page that already have that route, it remains the current slug.

<details>

_Fail to copy_

```gql
mutation {
  copyBindingContent(
    from: "e84d830f-8e31-4b38-97e4-87e2c3801929",
  	to: "bb2b7747-30d9-466e-9abe-2f90caf7150f",
    template: "powerplanet.store-theme@3.x:store.custom#faq",
    context: {
      id: "vtex.store@2.x:store.custom#test-cloning",
      type: "route",
    }
  ) {
    added {
      contentId
      origin
    }
    removed {
      contentId
      origin
    }
  }
}
```

</details>

---
## To do

- [] Add `I18n`
- [] Add better spinner on `BindingCloneModal`
- [] Refactor / Add error handling on `BindingCloneModal`
- [] Better error handler when saving content on `applyChanges` function
- [] Refactor HOC `withBindingsContent.tsx` to pass props via Context - Not a blocker, better readability
- [] Add feature to allow copy to specifics supportedLocales - Not a blocker, better UX
- [] Remove `Beta alert`

---
## Flow detail

1. Get route by id, domain `store` - on `withBindingsContext.tsx` - See Example #1;
- `routeId` example: `vtex.store@2.x:store.custom#test-new-page`
- `route.bindingId`: binding id where the page was created, but it's replaced if a new page is creates with the same slug;
- `route.conflicts`: routes with the same key `${declarer ?? 'null'}:${routeId}`. [Code](https://github.com/vtex/pages-graphql/blob/fix/copy-binding/node/v2/configurators/userConfigsSource.ts#L64-L101);

2. Mark bindings that already have the page as `overwrites` to warn users that page will be replaced - on `withBindingsContext.tsx > BindingFormatter > formattedState`

3. Generate state and dispatch functions to control state inside modal - Controls toggle, warnings about conflicts, disable checkbox when current, show `supportedLocales` - on `BindingSelector.tsx`

4. On click to copy - on `BindingCloningModal.tsx`:
- Check `overwrites`
  - Check `checkedItems` and the ones that will be overwritten;
  - Resolves if there is no items to be overwritten;
  - If so, open a modal for confirmation - resolves if accepeted, rejects if not.
- Open toast
- Run `applyChanges` function:
  - filter the bindings selected;
  - run `saveItem` on each selected binding - save one by one, if one fails, it stops the flow but the previously one that ran, will be created;
  - `saveItem` function:
    - Two branches:
      - `item.overwrites` is truthy:
        - Create an object to save `route`:
          - `routeInfo` properties - Example #1 - but conflicts (generated when getting a route) and uid;
          - `dataSource`: `vtex.rewriter`;
          - `bindingId`
        - Create object to copy content:
          - from: `currentBindingId`;
          - to: `item.id` (target `bindingId`)
          - template: routeInfo.blockId => `muji.store-theme@2.x:store.custom#branded-content`
          - context => `{ id: "vtex.store@2.x:store.custom#page-for-se", type: "route" }`
        - run `saveRoute` - Example #2
          - on `vtex.pages-graphql` [code](https://github.com/vtex/pages-graphql/blob/f018e7cfd1b8bf8558aa5a724f26c9a415b0186f/node/resources/userConfigsManager.ts#L353-L373):
            - find `userRoutes`
            - check for conflicts => throw if exists;
            - save it to rewriter;
            - Add route to pages Routes memo;
            - save to user route;
        - run `copyBindings` - Example #3
          - on `vtex.pages-graphql` [code](https://github.com/vtex/pages-graphql/blob/fix/copy-binding/node/v2/resolvers/copyBindingContent.ts#L29-L138)
            - `copBindingContent`:
              - Get `contentMap` - hash table with key and blocks information as value;
              - Get `userTemplates` - hash table templates as key and block name and block id as value;
              - Get tenant info - binding info;
              - Check if bindings provided as `to` and `from` are in the tenant info;
              - Get content target id `targetContentMapIds`;
              - Loop over all content and recreate `contentMap`;
              - When find a content id that matches the target id, loop over content to create content or replace it already exist, duplicating `messagesV2` to all content from `userTemplates`;
      - `item.overwrites` is falsy:
        - Skip `saveRoute` process;
        - Run the same steps listed above to copy content.


### Examples

- `# 1 - routeInfo` 

<details>

Query
```gql
{
  route(routeId: "vtex.store@2.x:store.custom#page-for-se", domain: "store") {
    auth
    blockId
    binding
    context
    declarer
    domain
    interfaceId
    conflicts {
      binding
      blockId
      interfaceId
      routeId
    }
    pages {
      pageId
      condition {
        id
        pageContext {
          id
          type
        }
        allMatches
        statements {
          subject
          verb
          objectJSON
        }
      }
      template
    }
    path
    routeId
    title
    uuid
    metaTags {
      description
      keywords
    }
  }
}
```

Response

```json
{
  "data": {
    "route": {
      "auth": false,
      "blockId": "muji.store-theme@2.x:store.custom#branded-content",
      "binding": "81f40404-5e9e-4d2b-8493-172394a0289c",
      "context": null,
      "declarer": null,
      "domain": "store",
      "interfaceId": "vtex.store@2.x:store.custom",
      "conflicts": [
        {
          "binding": "ff6d0d0c-9304-4df1-8853-8998383fbcb9",
          "blockId": "muji.store-theme@2.x:store.custom#branded-content",
          "interfaceId": "vtex.store@2.x:store.custom",
          "routeId": "vtex.store@2.x:store.custom#page-for-se"
        },
        {
          "binding": "81f40404-5e9e-4d2b-8493-172394a0289c",
          "blockId": "muji.store-theme@2.x:store.custom#branded-content",
          "interfaceId": "vtex.store@2.x:store.custom",
          "routeId": "vtex.store@2.x:store.custom#page-for-se"
        }
      ],
      "pages": [],
      "path": "/page-for-se",
      "routeId": "vtex.store@2.x:store.custom#page-for-se",
      "title": "page for se",
      "uuid": "mFvYnh3TX85CEDR3HR1vDQ",
      "metaTags": {
        "description": "",
        "keywords": []
      }
    }
  }
}
```

</details>

- `# 2 - saveRoute`

<details>

Request

```gql
mutation {
  saveRoute(route: {
    auth: false,
    blockId: "powerplanet.store-theme@3.x:store.custom#faq",
    context: null,
    declarer: null,
    domain: "store",
    interfaceId: "vtex.store@2.x:store.custom",
    path: "/test-cloning",
    routeId: "vtex.store@2.x:store.custom#test-cloning",
    pages: [],
    title: "test cloning",
    metaTags: {
      description: "",
      keywords: []
    },
    dataSource: "vtex.rewriter",
    bindingId: "e84d830f-8e31-4b38-97e4-87e2c3801929"

  }) {
     auth
    blockId
    binding
    context
    declarer
    domain
    interfaceId
    path
    routeId
    uuid
    metaTags {
      description
      keywords
    }
    title
  }
}
```

```json
{
  "data": {
    "saveRoute": {
      "auth": false,
      "blockId": "powerplanet.store-theme@3.x:store.custom#faq",
      "binding": "e84d830f-8e31-4b38-97e4-87e2c3801929",
      "context": null,
      "declarer": null,
      "domain": "store",
      "interfaceId": "vtex.store@2.x:store.custom",
      "path": "/test-cloning",
      "routeId": "vtex.store@2.x:store.custom#test-cloning",
      "uuid": "foqioG8kkncfZnP2qYfBpP",
      "metaTags": {
        "description": "",
        "keywords": []
      },
      "title": "test cloning"
    }
  }
}
```

</details>

- `# 3 - copyBindings`

<details>

```gql

mutation {
  copyBindingContent(
    from: "a9c6542d-f8a6-4f80-9465-e2de705d5e27",
  	to: "7cf38d3b-efa0-4d47-8201-d8b58cd4d3fd",
    template: "powerplanet.store-theme@3.x:store.custom#about-us",
    context: {
      id: "vtex.store@2.x:store.custom#new-clone-test",
      type: "route",
    }
  ) {
    added {
      contentId
      origin
    }
    removed {
      contentId
      origin
    }
  }
}

```

</details>