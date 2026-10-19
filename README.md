# Comment Replacer

A browser tool for one repetitive localization task: a reviewer leaves corrections as Word comments, and someone then has to copy each comment over the text it annotates. Comment Replacer does that for every comment you choose, and keeps the rest for manual handling.

**[Open the tool →](https://comment-replacer.vercel.app)**

I built it during my project-management internship at CLEAR Global (Translators without Borders), with AI-assisted coding. It cut about 85% of the manual effort on a recurring linguist task.

## How to use it

1. **Upload** a `.docx` file with comments.
2. **Review** the table: each row shows the annotated text and its comment. Mark each comment *auto* (replace the text with the comment) or *manual* (leave it for a person).
3. **Apply** and download `<name>_replaced.docx`. The summary shows how many replacements were applied, how many anchors were not found, and how many comments were kept for review.

## Privacy

Everything runs in your browser. The file is never uploaded: there is no server and no account. This matters for client documents under NDA.

## How it works

A `.docx` file is a zip of XML parts. The tool opens it with [JSZip](https://stuk.github.io/jszip/) and edits the WordprocessingML directly:

- `word/comments.xml` holds the comment texts. In `word/document.xml`, each comment's anchor is marked by `<w:commentRangeStart>` and `<w:commentRangeEnd>` with the same id.
- For each comment marked *auto*, the comment text goes into the first `<w:t>` text node inside the anchor, and the other text nodes in the range are emptied. Runs and their formatting stay in place, so the document structure isn't rebuilt.
- The comment markers and reference runs of applied comments are removed. If every comment was applied, `comments.xml` is emptied.

## Limitations

- The replacement takes the formatting of the anchor's first run. If the anchor mixes formats (for example, part bold), the new text uses the first part's format.
- An anchor that spans several paragraphs puts the whole replacement in the first paragraph and leaves the others empty.
- The tool doesn't handle tracked changes. Accept or reject them before you run it.

## Tech

One `index.html` file: vanilla JavaScript, DOMParser/XMLSerializer and JSZip 3.10.1 from cdnjs. It is deployed on Vercel.
