---
tags: [ linear, api, comments, documents ]
---

# Linear API: comments on documents can't be anchored

Linear's API does not let a comment on a document highlight a span of its text.
`commentCreate` lists `inlineAnchorFrom` and `inlineAnchorTo` (Y.js positions),
but the server rejects them with "Inline comments on documents are not
available". The Linear MCP connector has no anchor field at all.

The closest you can get is a comment with `quotedText` and `documentContentId`
and no anchor fields. Linear shows the quoted line on the comment, but nothing
in the document is highlighted.

**How to apply:** for line-by-line review of a Linear document, post one comment
per point, each quoting the line it answers. Don't edit the document to add
comment marks yourself. Tell the reader the comments aren't anchored.
