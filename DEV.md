# Dev


### Release
```bash
# run 
gh workflow run release.yml \
  --repo SpockOps/surak-action \
  --ref main \
  --field version=1.0.0 \
  --field dry_run=false
```