# Destination: Markdown File

Writes the report to a local `.md` file. This is the default destination — zero dependencies,
no MCP tools required. Always write here first before pushing to remote destinations.

---

## MCP Tool

None — uses `Write` (local file write)

---

## Config Fields

```yaml
- type: markdown-file
  path: report-output.md    # optional; defaults to report-output.md in CWD
```

---

## Write Instructions

1. Determine the output path:
   - Use `path` from config if specified
   - Otherwise default to `report-output.md` in the current working directory
2. If the file already exists, **append** the new sprint entry (do not overwrite the entire file)
3. Add a blank line separator between entries if appending
4. Write the formatted report as produced by the template

---

## Notes

- This destination is always included, even if not listed in config `destinations:`
- It serves as the local draft and archive entry
- After writing, confirm the path to the user so they can open or copy it
- The archive path (`archive.path` in config) is separate from this — append to the archive file after writing the output file
