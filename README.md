# `xbps-src` overlay demo

An `xbps-src` wrapper that (ab)uses `overlayfs` to trick `xbps-src` into building packages from different template repositories.

This is a very rough and rudimentary implementation, someone smarter and more experienced with shell scripting than me should figure out something better. Feel free to open issues and pull requests.

## Example usage

```sh
git clone https://github.com/freddylist/xbps-src-overlay-demo.git
cd xbps-src-overlay-demo/
ln -s path/to/void-packages distdir
mkdir repos && cd $_
ln -s path/to/some/srcpkgs 00-my-srcpkgs
ln -s path/to/some/more/srcpkgs 20-other-srcpkgs
# DON'T symlink void-packages/srcpkgs, it is automatically included
# because xbps-src relies on some packages from there.
cd ..
./xbps-src-wrapper pkg <package name from any linked repository>
```

As you might've guessed, repos are lexicographically sorted to determine their priority. `void-packages/srcpkgs/` has priority over every repo symlinked in the `repos/` directory. Repos with higher priority than `void-packages/srcpkgs/` should be symlinked in a `priority-repos/` directory.

By default, you'll find binary packages in `path/to/void-packages/hostdir/binpkgs/overlay-wrapper-pkgs`.

## How it works

We use `unshare` to be able to mount an `overlayfs` where `xbps-src` expects templates to be. Alternatively we could use `fuse-overlayfs`, but I think it might be slower.
