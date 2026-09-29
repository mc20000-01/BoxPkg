# BoxPkg

The BoxedLANG package registry. `umload` installs from here.

## Layout

```
registry.txt          the package index
packages/<name>.bx    one package file per entry
```

## registry.txt format

Pipe separated, one package per line. Blank lines and lines starting with `#`
are ignored.

```
name|raw url|version|description|author
```

`raw url` should point at `packages/<name>.bx` on the `main` branch, so a
package only has to be added once and every consumer can fetch it.

## Package file format

A package is an ordinary BX program. Metadata lives in leading `//` comments at
the very top of the file, before any code:

```bx
// name: mypack
// version: 1.0.0
// author: yourname
// description: what it does
// deps: otherpack, thirdpack

// The rest is normal BX.
```

* `deps` is a comma separated list of other packages, or `none`.
* Dependencies are installed automatically when the package is required.
* Dependency cycles are detected and reported instead of looping.

## Using a package from BX

`umload require|<name>` installs the package if needed, resolves its
dependencies, and runs it. The usual idiom is to set input boxes, require the
package, then read the output box:

```bx
box st_in|The Quick Brown Fox
box st_op|slugify
umload require|strutil
say $st_out
```

See `strutil` for a worked example.

## umload commands

```
umload require|name        install if needed, resolve deps, run the package
umload install|name|url    install from a url, the registry, or ./packages
umload list                loaded and cached packages
umload info|name           metadata for one package
umload deps|name           dependency tree
umload verify|name         install dependencies without running
umload search|query        search the registry
umload remove|name         delete a cached package
umload cache|dir           print the cache directory
umload cache|clear         empty the cache
umload publish|name|ver    copy into this repo, update the registry, push
```

## Cache

Packages are cached in `$BOXEDLANG_CACHE`, or `~/.cache/boxedlang`, or
`./.boxcache` if neither is writable.

## Contributing

1. Add your package to `packages/`.
2. Add a line to `registry.txt`.
3. Commit and push to `main`.
4. Verify it installs: `umload install|<name>` from a clean cache.
