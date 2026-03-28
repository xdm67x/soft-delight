---
name: update-changelog
description: Update CHANGELOG.md file based on git commits using conventional commits format. Use this skill when the user asks to update the changelog, add entries to changelog, generate changelog from commits, or mentions maintaining changelog documentation. Trigger for phrases like "update changelog", "add to changelog", "generate changelog", "changelog from commits", or when discussing release notes.
---

# Update Changelog Skill

This skill helps maintain a CHANGELOG.md file following the Keep a Changelog format, automatically generating entries from conventional commits.

## When to Use

Use this skill when:
- User asks to update the changelog
- User wants to add entries based on recent commits
- User mentions generating changelog from git history
- Preparing for a release and need to document changes

## Workflow

### Step 1: Read current version from package.json

Read package.json to get the current version number:
- Extract the `version` field
- This version will be used for the new changelog section

### Step 2: Analyze git history

Read recent commits to understand what changes need to be documented:

```bash
git log --pretty=format:"%H|%s|%b" --no-merges -20
```

Filter commits since the last changelog update by comparing dates, or use commits since the last version tag.

### Step 3: Parse conventional commits

Parse commits following the conventional commits specification:
- `feat:` → Added section
- `fix:` → Fixed section
- `docs:` → Changed section (documentation updates)
- `style:` → Changed section (formatting)
- `refactor:` → Changed section
- `perf:` → Changed section (performance improvements)
- `test:` → Changed section (tests)
- `chore:` → Changed section (maintenance)
- `BREAKING CHANGE:` in commit body → separate Breaking Changes section

### Step 4: Read existing changelog

Read the current CHANGELOG.md to understand its structure and find where to insert new entries.

### Step 5: Update changelog

Insert a new version section at the top of the changelog (after the header), using the version from package.json and today's date:

```markdown
## [VERSION] - YYYY-MM-DD

### Added
- Description from feat commits (without the "feat:" prefix)

### Fixed
- Description from fix commits (without the "fix:" prefix)

### Changed
- Description from other conventional commits

### Breaking Changes
- Description of breaking changes (if any)
```

Format rules:
- Use the version from package.json in format: [X.Y.Z]
- Date format: YYYY-MM-DD
- Each entry starts with "- " followed by the commit message (remove conventional commit prefix)
- Capitalize the first letter of each entry
- Group entries by type (Added, Fixed, Changed, Breaking Changes)
- Remove duplicate entries
- Sort entries alphabetically within each section
- Do NOT include commit hashes in the changelog entries

### Step 6: Update version references (optional)

If needed, add version comparison links at the bottom:

```markdown
[VERSION]: https://github.com/OWNER/REPO/compare/vPREVIOUS...vVERSION
```

## Examples

**Input:**
- package.json version: "1.1.0"
- Commits:
```
feat: add dark mode support
fix: resolve memory leak in parser
feat: implement user authentication
docs: update API documentation
```

**Output changelog section:**
```markdown
## [1.1.0] - 2024-03-28

### Added
- Add dark mode support
- Implement user authentication

### Fixed
- Resolve memory leak in parser

### Changed
- Update API documentation
```

## Important Notes

- Always read package.json first to get the correct version
- Always read the existing changelog to maintain consistent formatting
- Preserve any existing content and structure
- If no conventional commits are found, ask the user how to proceed
- Never remove existing changelog entries
- If the changelog doesn't exist, create it with the standard Keep a Changelog header
- Use today's date for new versions
- The skill should be idempotent - running it multiple times should not duplicate entries
- Skip commits that are already documented in the changelog
