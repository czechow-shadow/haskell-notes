# Haskell-notes

## Compiling statically

### GCC

```
gcc main.c -I $HOME/opt/xz-5.2.5/include -L $HOME/opt/xz-5.2.5/lib -Wl,-Bstatic -llzma -Wl,-Bdynamic
```
or

```
gcc main.c -I $HOME/opt/xz-5.2.5/include -L $HOME/opt/xz-5.2.5/lib -l:liblzma.a
```

### GHC

```
ghc -I$(HOME)/opt/xz-5.2.5/include -L$(HOME)/opt/xz-5.2.5/lib \
    -l:liblzma.a pc.c Main.hs
```

Side note: To see gcc options I had to use `strace -s 1024 -f` ....

See also <https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/phases.html>

### Cabal

Note that --cabal-verbose and --verbose are somehow unpredictable in what they output, at least for me.
Anyhow, the following worked:

```
stack build --cabal-verbose --extra-include-dirs=$HOME/opt/xz-5.2.5/include --extra-lib-dirs=$HOME/opt/xz-5.2.5/lib --verbose 2>&1 | tee log.txt
```

with cabal:

```
cabal-version:       >=1.10
name:                hask-static-lzma
version:             0.1.0.0
build-type:          Simple

library
  exposed-modules:     Foo
  -- C bits
  c-sources:           pc.c
  build-depends:       base
  includes:            lzma.h
  -- Note the colon and the full lib name (works with gnu binutils >= 2.18)
  extra-libraries:     :liblzma.a


executable hask-static-lzma
  main-is:             Main.hs
  build-depends:       base
                     , hask-static-lzma
  default-language:    Haskell2010
```



