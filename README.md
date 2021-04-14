# generate-pkgbuild-action

This action generates PKGBUILD of the go-program for `makepkg`.

```yaml
- name: Make PKGBUILD
  if: startsWith(github.ref, 'refs/tags/')
  uses: kyoh86/generate-pkgbuild-action@v1
  with:
    # (REQUIRED) License name
    license: mit

    # An email address of the author
    email: me@kyoh86.dev

    # A description for the package
    description: "A sample package"

    # Call `go run -tags man ./cmd/{entrypoint} man` to generate man page
    makeman: true
```

A sample of the full-workflow:

```yaml
name: Generate PKGBUILD
on:
  push:
    tags:
      - 'v*.*.*'

jobs:
  generate-pkgbuild:
    name: Generate PKGBUILD
    runs-on: ubuntu-latest
    if: startsWith(github.ref, 'refs/tags/')
    steps:
      - name: Checkout Sources
        uses: actions/checkout@v2

      - name: Make PKGBUILD
        if: startsWith(github.ref, 'refs/tags/')
        uses: kyoh86/generate-pkgbuild-action@v1
        with:
          # (REQUIRED) License name
          license: mit

          # An email address of the author
          email: me@kyoh86.dev

          # A description for the package
          description: "A sample package"

          # Call `go run -tags man ./cmd/{entrypoint} man` to generate man page
          makeman: true

      - name: Check PKGBUILD
        if: startsWith(github.ref, 'refs/tags/')
        uses: edlanglois/pkgbuild-action@v1

      # If you want to upload PKGBUILD file to a release asset,
      # use softprops/action-gh-release.
      - name: Upload PKGBUILD to a release asset
        uses: softprops/action-gh-release@v1
        if: startsWith(github.ref, 'refs/tags/')
        with:
          files: "*_PKGBUILD.tar.gz"
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      # You also can upload PKGBUILD file to an artifact
      - name: Upload PKGBUILD to an artifact
        uses: actions/upload-artifact@v2
        with:
          name: package
          path: "*_PKGBUILD.tar.gz"
```
