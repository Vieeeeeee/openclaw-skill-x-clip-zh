---
name: x-clip-zh
description: Translate or clip a user-provided X/Twitter or web link into Chinese notes (if source is already Chinese, keep original text), save as a Markdown clipping into an Obsidian vault's `Clippings` folder, include source images when present, verify file visibility in the real vault path, then report completion with a ~200 Chinese-character summary. Use when the user says things like "翻译这个链接放到 Obsidian/笔记"、"把这个推文存到 Obsidian"、"剪藏这条推文"、"把这个链接收进笔记"、"保存这篇文章到笔记库"、"把这个网页存到笔记"、"帮我收藏这篇文章到 Obsidian"、"把这条推文剪藏到 Obsidian"、"链接存到 Obsidian"、"帮我把这个链接保存到笔记库"、"把这个网页内容保存到笔记"、"链接归档到 Obsidian"、"把这个帖子存到笔记软件"、"帮我把链接 clip 到 Obsidian"、"翻译并归档这个链接"、"把这条内容存进笔记库"、"网页剪藏 Obsidian"、"链接保存 Obsidian" or asks for link-to-note or link-to-Obsidian clipping workflows.
---

# X Clip Zh

## Workflow
1. Fetch source content from the URL.
2. If direct fetch is blocked or incomplete, fallback to `https://r.jina.ai/http://<original-url-without-scheme>`.
3. Extract main text and title; ignore ads, navigation, and footer clutter.
4. For X/Twitter links, also check whether the post contains media.
   - If images exist, download them locally instead of hotlinking.
   - Save images beside the note under `Clippings/assets/`.
   - Prefer stable local embeds in the Markdown note so images render inside Obsidian even if the source changes.
   - If multiple images exist, preserve their order and place them as close as possible to their original positions in the source, not all piled at the top.
   - Use a single cover image near the top only when the source clearly has a header or lead image there.
   - If exact positions cannot be recovered, distribute images by section or paragraph context so reading flow still makes sense.
   - If no media exists, continue with text-only clipping.
5. Detect language:
   - If content is Chinese, keep text as-is with light cleanup only.
   - If content is non-Chinese, default to a strict, faithful Chinese translation that preserves the original paragraph order, structure, scope, and claims.
   - Do not rewrite into a commentary article, condensed interpretation, or adapted essay unless the user explicitly asks for a summary, adaptation, or rewrite.
   - You may smooth wording for natural Chinese, but do not merge multiple source paragraphs into one opinionated rewrite and do not add new framing that was not present in the source.
6. Build the Markdown note with this layout:
   - YAML front matter (see below)
   - `# <title in Chinese>`
   - Body content (translated or copied Chinese)
   - `## 200字摘要` section (target 180-220 Chinese chars)

   **YAML front matter format (required for all notes):**
   ```yaml
   ---
   title: <title in Chinese>
   source: <original URL>
   author: <author name if known>
   published: <publication date if known (YYYY-MM or YYYY-MM-DD)>
   clipped: <clipped date (YYYY-MM-DD)>
   tags:
     - <relevant tag 1>
     - <relevant tag 2>
   created: <clipped date (YYYY-MM-DD)>
   ---
   ```
   - Include local image embeds after the title if media was downloaded (e.g. `![[assets/filename.jpg]]`).
   - Do NOT use inline metadata lines like `**来源:**` or `**日期:**` — all metadata goes in the front matter.

## Obsidian Save Rules
1. Read `~/Library/Application Support/obsidian/obsidian.json` and resolve the user-specified target vault.
2. Save notes to `<target vault>/Clippings/`.
3. Save downloaded images to `<target vault>/Clippings/assets/`.
4. Filename format: `YYYY-MM-DD-<short-topic>.md`.
5. Image filename format: `YYYY-MM-DD-<short-topic>-1.<ext>`, `-2.<ext>` ...
6. If a duplicate filename exists, append `-v2`, `-v3`, etc.; media filenames should stay aligned with the final note basename.
7. Use Obsidian-local embeds like `![[assets/<filename>]]`, not remote image URLs, whenever local media was downloaded.

## Verification
1. Confirm the note file exists in the real vault path.
2. If media was present, confirm the downloaded image files also exist in the real vault path.
3. Quickly verify the note body contains the expected local image embed lines.
4. Provide a final report with:
   - Saved absolute path
   - Source URL
   - Completion status (`完成`)
   - Whether images were saved (`有图` / `无图`)
   - The same ~200-character Chinese summary

## Quality Bar
- Keep translations concise and readable; avoid machine-literal wording.
- Preserve factual accuracy; do not invent missing details.
- Default output for non-Chinese sources is faithful translation first, not paraphrase-first.
- Preserve the source's sectioning and argument flow as much as the available extraction allows.
- If you also want to add interpretation or a rewritten note-style version, keep it clearly separate from the translation.
- For X clips, images are part of the clipping, not optional decoration. If the source has meaningful images, save and embed them by default.
- Readability rule: image placement must preserve reading flow. Dumping all images at the top is considered a bad clip and should be fixed before reporting completion.
- Prefer auto-detection first, then ask focused questions only when the environment is ambiguous.
- If the environment is missing prerequisites, stop with a concrete explanation rather than faking completion.
- Do not run external actions beyond fetch, read, write, and verification unless explicitly requested.
