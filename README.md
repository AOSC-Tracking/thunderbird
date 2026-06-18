# Tracking repository for Thunderbird

## Standard Operation Procedure

1. Download a source archive of Thunderbird from <https://archive.mozilla.org/pub/thunderbird/>. Normally the URL is <https://archive.mozilla.org/pub/thunderbird/releases/$VER/source/thunderbird-$VER.source.tar.xz>, replacing "$VER" with the version to be imported, like `128.0.1esr`.

```sh
export VER='128.0.1esr'
```

2. Switch to the `tarball` branch, then remove all files and directories in the working tree, including the hidden one, except the `.git` directory:

```sh
git checkout tarball
find . -mindepth 1 -maxdepth 1 \( \! -name .git \) -exec rm -rf {} +
```

3. Extract all files from the archive, then move all files and directories, including the hidden one, from the `thunderbird-$VER` directory to the working tree.

```sh
tar --strip-component=1 -pxvf thunderbird-"$VER".source.tar.xz
```

4. Recursively remove `.gitattributes` files, `.gitignore` files, `.gitmodules` files and `.git` directories except the one in the top-level.

```sh
find . \( -name .gitattributes -o -name .gitignore -o -name .gitmodules \) -delete
find . -mindepth 2 -type d -name .git -exec rm -rf {} +
```

5. Update the index using all files in the entire working tree, then create a new commit with message of "chore: import v$VER".

```sh
git add -A
git commit -m "chore: import v$VER"
```

6. Create a lightweight tag with name of "v$VER".

```sh
git tag "v$VER"
```

7. Push the `tarball` branch and the tag just created to the remote.

```sh
git push
git push --tags
```

8. Create a new branch with name of "aosc/v$VER", pointing to the current `HEAD`, then switch to it.

```sh
git checkout -b "aosc/v$VER"
```

9. Apply all needed commits and/or patches.
10. Create a lightweight tag with name of "aosc/v$PKGEPOCH%$PKGVER-$PKGREL". If any of these variables equals zero, omit the related section and the corresponding separator. For the definition of these variables, refer to [autobuild4](https://github.com/AOSC-Dev/autobuild4).

```sh
git tag "aosc/v$PKGEPOCH%$PKGVER-$PKGREL"
```

11. Push the `aosc/v$VER` branch and the tag just created to the remote.

```sh
git -c 'push.autoSetupRemote=true' push
git push --tags
```

12. Prepare patches for the ABBS tree.

```sh
git format-patch "tags/v$VER..tags/aosc/v$PKGEPOCH%$PKGVER-$PKGREL"
```

## References

- [Downloading Source Archives](https://github.com/mdn/archived-content/blob/main/files/en-us/mozilla/developer_guide/source_code/downloading_source_archives/index.html)
  - [Wayback Machine](https://web.archive.org/web/20210426034753/https://developer.mozilla.org/en-US/docs/Mozilla/Developer_guide/Source_Code/Downloading_Source_Archives)
