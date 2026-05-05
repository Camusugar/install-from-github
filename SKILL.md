---
name: install-from-github
description: Install skills from GitHub repositories.
---

# Install Skill from GitHub

Install skills from a GitHub repo into `~/.claude/skills/`.

## Workflow (3 tool calls)

### 1. Clone + scan + check existing (single Bash call)

Determine `<owner>` and `<repo>` from the URL. Compute `T="/tmp/claude-skill-install-<owner>-<repo>"`. Chain all operations with `&&`.

```bash
T="/tmp/claude-skill-install-<owner>-<repo>"
rm -rf "$T" && git clone --depth 1 https://github.com/<owner>/<repo>.git "$T" && \
for f in $(find "$T" -maxdepth 3 -name SKILL.md); do
  d=$(dirname "$f")
  n=$(sed -n '/^---$/,/^---$/p' "$f" | sed -n 's/^name: *//p' | head -1)
  [ -z "$n" ] && n=$(basename "$d")
  s=${d#"$T"/}
  [ -d ~/.claude/skills/"$n" ] && i=1 || i=0
  echo "$n|$s|$i"
done > "$T.skills"
```

Output: `$T.skills` with lines `name|source|installed`. If empty → no skills found, report and stop.

- Add `--branch <ref>` for non-default branches.
- For large repos, use `run_in_background: true`.

**HARD RULE:** Never split clone, find, and extract into separate calls. One Bash call only.

### 2. Checkbox selection

Read `$T.skills`. Build `AskUserQuestion` with `multiSelect: true`. Each question must have a unique `question` field. Max 4 questions per call, 2-4 options each → up to 16 per call. Split across multiple calls if >16. Prefix existing skills with `⚠` in the label.

### 3. Install selected + cleanup (single Bash call)

```bash
T="/tmp/claude-skill-install-<owner>-<repo>"
for name in <selected_names>; do
  s=$(grep "^$name|" "$T.skills" | head -1 | cut -d\| -f2)
  rm -rf ~/.claude/skills/"$name" && cp -r "$T/$s" ~/.claude/skills/"$name"
done && rm -rf "$T" "$T.skills"
```

**HARD RULE:** Install and cleanup in one call. Never separate them.

Report: `Installed N skill(s): name1 name2 ...`
