# Square Zhong's Blog

This is a Hugo site deployed to GitHub Pages. The build uses Hugo Extended 0.164.0 and the PaperMod Git submodule commit recorded by this repository.

## Theme updates

The deployment workflow deliberately does **not** follow PaperMod's `master` branch. This keeps every deployment reproducible: a commit is always built with the PaperMod commit recorded in `themes/PaperMod`.

To update PaperMod intentionally, run the following from the repository root:

```bash
# Check out the PaperMod commit currently recorded by the repository.
git submodule update --init --recursive

# Fetch and inspect the available upstream changes.
git -C themes/PaperMod fetch origin
git -C themes/PaperMod log --oneline HEAD..origin/master

# Move to the tested upstream revision. Use a specific commit SHA instead of
# origin/master if you need to select a particular revision.
git -C themes/PaperMod checkout origin/master

# Build and review the site before committing the new submodule pointer.
hugo --gc --minify --baseURL "https://squarezhong.github.io/"
git diff --submodule=log

# Record the exact PaperMod commit used by subsequent CI deployments.
git add themes/PaperMod
git commit -m "chore(theme): update PaperMod"
```

After the commit is pushed, GitHub Actions checks out that exact submodule revision. To confirm the revision locally, run `git submodule status`; it should show no leading `+` or `-` marker.
