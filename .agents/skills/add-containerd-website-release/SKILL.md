---
name: add-containerd-website-release
description: Workflow for adding a new containerd release to the containerd.io website, including adding git submodules, updating config.toml, netlify.toml, and docs root page.
---
# Adding a containerd Release to the Website

This skill provides a structured workflow for adding documentation for a new containerd release to the `containerd.io` website.

## Workflow Overview

1.  **Add Git Submodule**: Add the new release branch as a submodule.
2.  **Update `config.toml`**: Update the versions list. Also set the new version as latest if it is the newest release.
3.  **Update `netlify.toml`**: Update the redirect rule for `/docs/latest/*` if this is the newest release.
4.  **Refresh Documentation**: Run `make refresh-docs` to pull and process content.
5.  **Update Docs Root Page**: Add a link to the new version on the main docs page.
6.  **Verify**: Build the site and check for errors.

## 1. Add Git Submodule

Add the new release branch from the `containerd/containerd` repository as a submodule.

```bash
# Replace X.Y with the version (e.g., 2.3)
git submodule add -b release/X.Y https://github.com/containerd/containerd.git containerd-X.Y
```

## 2. Update `config.toml`

Modify `config.toml` to include the new version.

Locate the `[params.versions]` section and add the new version to the `all` list:
```toml
[params.versions]
all = ["2.3", "2.2", "2.1", "1.7", "main"]
```

**If this is the new latest release**, also update `latest` and `latestdir`:
```toml
latest = "2.3.0" # Update to the new full version
latestdir = "2.3" # Update to the new major.minor
```

## 3. Update `netlify.toml`

**If this is the new latest release**, update the redirect rule for `/docs/latest/*` to point to the new version.

Locate the redirects section:
```toml
[[redirects]]
from = "/docs/latest/*"
to = "/docs/2.3/:splat" # Update to the new version
status = 302
```

## 4. Refresh Documentation

Run the script to copy and process documentation from the submodule.

```bash
make refresh-docs
```

## 5. Update Docs Root Page

Add a link to the new version in `content/docs/_index.md`.

Locate the "Getting Started" section and add the new version in the appropriate place (usually in descending version order):
```markdown
- [main](main/getting-started/)
- [2.3.x](2.3/getting-started/)
- [2.2.x](2.2/getting-started/)
...
```

## 6. Verification

Verify that the site builds correctly.

```bash
# Run a local build
hugo

# Or serve locally to check in browser
make serve
```

Check that `content/docs/X.Y/` was created and contains the expected documentation files.
