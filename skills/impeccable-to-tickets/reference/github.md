# Publishing to GitHub

Verified against `gh` 2.81.0. `gh` infers the repo from `git remote -v` when run inside a clone; set `REPO=<owner>/<name>` for the `gh api` calls, which do not infer.

Confirm auth before writing anything: `gh auth status`.

## Bodies go in files, never in `--body`

Multi-line bodies with backticks, `$`, quotes, and `!` break under shell quoting, and a heredoc inside `"$(...)"` fails on apostrophes. Write each body with the file-writing tool, then:

```bash
gh issue create --title "..." --body-file /tmp/imp/01-slug.md --label "a" --label "b"
```

`gh issue create` prints the issue URL. The number is the last path segment.

## Labels, created on demand

```bash
create_label() {
  gh label create "$1" --color "$2" --description "$3" 2>/dev/null \
    && echo "created  $1" || echo "exists   $1"
}
create_label "impeccable:harden" "D93F0B" "Fix with /impeccable harden"
```

`gh label create` fails when the label exists, which is why the `||` branch is there. Never `--force`; it would overwrite a colour or description the user chose.

## Sub-issues

GitHub's sub-issue API takes the child's **numeric database id**, not its `#number` and not its `node_id`.

```bash
REPO=owner/name
PARENT=37

make_sub() {
  local title="$1" file="$2"; shift 2
  local labelargs=()
  for l in "$@"; do labelargs+=(--label "$l"); done
  local url num id
  url=$(gh issue create --title "$title" --body-file "$file" "${labelargs[@]}")
  num=${url##*/}
  id=$(gh api "repos/$REPO/issues/$num" --jq .id)
  gh api --method POST "repos/$REPO/issues/$PARENT/sub_issues" \
    -F sub_issue_id="$id" --jq '.number' > /dev/null
  echo "#$num  $title"
}

make_sub "[P1] Title here" /tmp/imp/01-slug.md "impeccable:harden" "bug" "ready-for-agent"
```

`gh` has no native sub-issue command; the REST endpoint is the way. If it returns 404 or 422, sub-issues are unavailable on that repo: fall back to a task list in the parent body plus `Part of #<parent>` at the top of each child, and tell the user the relationship is textual.

## Blocking dependencies

Native issue dependencies, which the UI shows and which gate the frontier. Also keyed on the blocker's numeric database id.

```bash
block() {
  local child=$1 blocker=$2
  local bid
  bid=$(gh api "repos/$REPO/issues/$blocker" --jq .id)
  if gh api --method POST "repos/$REPO/issues/$child/dependencies/blocked_by" \
       -F issue_id="$bid" --jq '.number' >/dev/null 2>&1; then
    echo "ok    #$child blocked by #$blocker"
  else
    echo "FAIL  #$child blocked by #$blocker (unavailable, body line stands)"
  fi
}
block 45 44
```

The `Blocked by` line in the body is written either way. It is the human-readable record and the fallback when dependencies are unavailable.

## Forward references

A ticket cannot reference a blocker that does not exist yet. Write the unblocked tickets' bodies with a placeholder token, create the unblocked tickets first, then substitute real numbers before creating the blocked ones:

```bash
sed -i '' 's|BLOCKER_DESCRIBEDBY|#44|' /tmp/imp/08-slug.md
```

On Linux, `sed -i` without the empty-string argument.

## Verify the tree

Always read it back and show the user:

```bash
gh api "repos/$REPO/issues/$PARENT" \
  --jq '"#\(.number) \(.title) — sub_issues: \(.sub_issues_summary.total)"'

gh api "repos/$REPO/issues/$PARENT/sub_issues" \
  --jq '.[] | "  #\(.number)  blocked_by=\(.issue_dependencies_summary.blocked_by)  [\(.labels|map(.name)|join(", "))]  \(.title)"'
```

`issue_dependencies_summary.blocked_by` counts **open** blockers only, so it is the live gate: a ticket is ready when the count reaches zero.

## Check for prior art first

The repo may already have a convention for this. Before writing anything:

```bash
gh issue list --state all --limit 20 --json number,title,labels \
  --jq '.[] | "\(.number) [\(.labels|map(.name)|join(","))] \(.title)"'
```

If an earlier audit or critique batch exists, open its parent and one child and match their structure. A consistent tracker beats a better template.
