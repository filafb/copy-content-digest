# Copy Content Digest

## Questions

1. The migration generates two outputs A. saves old content in the new `messages` format, B. generates a new content JSON.
- We noticed that since `vtex.pages-graphql@2.108.0` user content is already saved in the new `messages` format. Can we assume the migration is only needed for content created before that version?
- What is the final goal of the new generated content file? And where should it be saved?

```
http://vbase.aws-us-east-1.vtex.io/${account}/${workspace}/buckets/vtex.pages-graphql/userData/files/store/content.json`
```
- Any known issue wih the script?
- What would be a good way to test it? Or what are the issues to pay attention when running it?
- Should we transform the migration in an IO APP?


2. Muji has `vtex.pages-graphql@1.x`. What is the potential breaking changes to `@2.x`?

3. Should we make a PR pointing to the current WIP branches or keep working on them? Can the CMS team rebase the branches?

4. `UX`: Should we show the copy button only for custom pages?

## Next tasks

- [ ] Add `I18n`
- [ ] Add better spinner on `BindingCloneModal`
- [ ] Refactor / Add error handling on `BindingCloneModal`
- [ ] Better error handler when saving content on `applyChanges` function
- [ ] Refactor HOC `withBindingsContent.tsx` to pass props via Context - Not a blocker, better readability
- [ ] Add feature to allow copy to specifics supportedLocales - Not a blocker, better UX
- [ ] Remove `Beta alert`
- [ ] Investigate and fix possible bugs found

## Migration Script
- [TL;DR](https://github.com/filafb/copy-content-digest/blob/main/docs/migrate-flow.md#tldr)
- [Questions](https://github.com/filafb/copy-content-digest/blob/main/docs/migrate-flow.md#questions)
- [To investigate](https://github.com/filafb/copy-content-digest/blob/main/docs/migrate-flow.md#to-investigate)
- [To do](https://github.com/filafb/copy-content-digest/blob/main/docs/migrate-flow.md#to-do)
- [Flow detail](https://github.com/filafb/copy-content-digest/blob/main/docs/migrate-flow.md#flow-detail)
- [To remember](https://github.com/filafb/copy-content-digest/blob/main/docs/migrate-flow.md#to-remember)


---

## Copy Content feature
- [Questions](https://github.com/filafb/copy-content-digest/blob/main/docs/copy-content-flow.md#questions)
- [To investigate](https://github.com/filafb/copy-content-digest/blob/main/docs/copy-content-flow.md#to-investigate)
- [To do](https://github.com/filafb/copy-content-digest/blob/main/docs/copy-content-flow.md#to-do)
- [Flow detail](https://github.com/filafb/copy-content-digest/blob/main/docs/copy-content-flow.md#flow-detail)
