# Confirmed project versions

Use this reference only when the user asks to confirm, compare, roll back, clean up, or release a version.

## Version registry

Store confirmed versions in `Agent work/shared/versions/registry.yaml`:

```yaml
active_version: v1.0.0
versions:
  - id: v1.0.0
    confirmed_at: ISO-8601 timestamp
    confirmed_by: user
    scope: concise description
    deliverables: [relative/path]
    rollback:
      type: git_tag | snapshot
      reference: tag-name-or-snapshot-path
    notes: concise change summary
```

## Git projects

After explicit confirmation, stage only the approved scope, create a commit, and create a version tag. Record both commit SHA and tag in the registry. Never commit unrelated user changes.

## Non-Git projects

Copy only approved deliverables and explicitly changed project files into a dated version snapshot. Record a manifest with original paths and hashes. Do not snapshot large or unrelated directories.

## Cleanup

Delete only files inside `Agent work/.tmp/` automatically after a successful confirmed version. For every other document, write a cleanup candidate with a reason and proposed `archive` or `delete` action. Require explicit user confirmation before modifying candidates.
