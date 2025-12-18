# git-summarizer

A tiny Ruby script that copies a git diff and a prompt to your clipboard so you can paste it into an LLM for fast summaries.

[![preview](https://asciinema.org/a/762705.svg)](https://asciinema.org/a/762705)

## How it works

It behaves differently based on whether you have staged changes:

- **Staged changes**: generates a prompt to produce a concise commit message (≤ 80 chars) with an optional second line description.
- **No staged changes**: generates a prompt to write a pull request description using a simple Markdown template.

The clipboard contains the diff, a separator line, then the prompt.

## Install

1. Make the script executable:

```bash
chmod +x summarize
```

2. Run it from this repo, or add a symlink to a directory on your PATH:

```bash
ln -s "$(pwd)/summarize" ~/bin/git-summarize
```

Make sure `~/bin` is on your PATH if you use the symlink approach.

## How to use

1. Make sure you're on macOS (this uses `pbcopy` for the clipboard).
2. Run the script from inside a git repository.

```bash
./summarize
# or if symlinked
# git-summarize
```

3. Paste the clipboard contents into your LLM of choice.

### Common workflows

- **Commit message**
  1. Stage your changes: `git add -A`
  2. Run the script
  3. Paste into your LLM and use the response as your commit message

- **Pull request description**
  1. Make sure no changes are staged
  2. Run the script
  3. Paste into your LLM and use the response as your PR description

If you want the script to talk directly to OpenAI, create a `.env` file in this repo that includes your API key and consent preference:

```
OPENAI_SECRET_KEY=sk-...
APPROVE_AI_REQUEST=0 # set to 1 to skip the consent prompt
```

With `APPROVE_AI_REQUEST=0` (or unset) the script will ask your consent before sending your diff to the API.

## Example output

Commit message mode:

```text
diff --git a/app.rb b/app.rb
index 5c3b2f1..8f2c4d0 100644
--- a/app.rb
+++ b/app.rb
@@ -1,3 +1,3 @@
-puts "hello"
+puts "hello world"
======================
Instruction:
  - Write a commit message summarizing the change in ≤ 80 characters.
  - Optionally add a short description if more explanation is needed.
  - Do not include labels like "Commit Message" or "Description".
  - Output is strict: line 1 is the commit message; if present, line 2 is the description, with no blank lines.
```

Pull request description mode:

```text
diff --git a/app.rb b/app.rb
index 5c3b2f1..8f2c4d0 100644
--- a/app.rb
+++ b/app.rb
@@ -1,3 +1,3 @@
-puts "hello"
+puts "hello world"
======================
Instruction:
  - Write a Pull Request description using the Template below.
  - Output as a plain-text Markdown code block.
Template:
## What
Briefly describe what changes this pull request introduces.
## Why
Explain why this pull request is necessary, clearly and concisely.
## How
List how the changes were implemented, using short bullet points.
```

## Notes

- Uses the default branch from `origin/HEAD` when nothing is staged, with a fallback to `main` or `master` if needed.
- Update the script in `summarize` if you want a different clipboard tool.
