## Testing

Besides `autotools` you will need to have `cmake` installed.

Here's a summary of commands required to run tests:

```
cd gumbo-parser
./autogen.sh
./configure
rm -rf gtest
git clone --depth 1 https://github.com/google/googletest.git -b v1.15.2 gtest
make check
```

Gumbo's `make check` automatically builds gtest and then links in the library.

If you have gtest and its development files installed via package manager system
wide, then you can omit `git clone` command and run `make check` straightaway
not having directory `gtest` in project root.

## Python tests

```sh
PYTHONPATH=python python3 -m gumbo.gumboc_test
PYTHONPATH=python python3 -m gumbo.soup_adapter_test  # requires bs4
```
alternatively
```sh
python3 -m unittest discover -s python -p '*test.py'
```

## Web Platform Tests tree construction tests

```sh
git submodule update --init
PYTHONPATH=python python3 -m gumbo.html5lib_adapter_test  # requires html5lib
```

## Fuzzing

```
export CC=clang CXX=clang++
```
```
meson setup --wipe builddir --buildtype=debug -Dfuzz=true
```
```
meson compile -C builddir
```
```
./builddir/gumbo_fuzz -jobs=4 fuzz/corpus
```
