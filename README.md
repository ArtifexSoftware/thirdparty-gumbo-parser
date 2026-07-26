## Gumbo - HTML parser library implemented in C99

Gumbo is an implementation of the HTML5 parsing algorithm implemented
as a pure C99 library with no outside dependencies. It's designed to serve
as a building block for other tools and libraries such as linters,
validators, templating languages, and refactoring and analysis tools.
This repository adheres to all the original ideas of the archived
[GitHub repository](https://github.com/google/gumbo-parser), which has not seen
any development since 2016.

Goals & features:

* Full compliance with the [HTML5 spec](https://html.spec.whatwg.org/multipage).
* Robust and resilient to bad input.
* Simple API that can be easily wrapped by other languages.
* Support for source locations and pointers back to the original text.
* Support for fragment parsing.
* Conformance with the [Web Platform Tests tree construction test suite](https://github.com/web-platform-tests/wpt/tree/master/html/syntax/parsing/resources).
* Relatively lightweight, with no outside dependencies.
* Tested on over 2.5 billion pages from Google's index.
* Follows [Semantic Versioning](https://semver.org) scheme.

Non-goals:

* Execution speed.  Gumbo gains some of this by virtue of being written in
  C, but it is not an important consideration for the intended use-case, and
  was not a major design factor.
* Support for encodings other than UTF-8.  For the most part, client code
  can convert the input stream to UTF-8 text using another library before
  processing.
* Mutability.  Gumbo is intentionally designed to turn an HTML document into a
  parse tree, and free that parse tree all at once.  It's not designed to
  persistently store nodes or subtrees outside of the parse tree, or to perform
  arbitrary DOM mutations within your program.  If you need this functionality,
  we recommend translating the Gumbo parse tree into a mutable DOM
  representation more suited for the particular needs of your program before
  operating on it.

## Build

```
meson setup build
meson compile -C build
meson test -C build
meson install -C build
```

## Example

```
example.c
```
```c
#include <gumbo.h>
#include <stdio.h>

int main() {
	GumboOutput *output = gumbo_parse("<h1>Hello, World!</h1>");

	const GumboNode *stack[64] = {output->root};
	int depth[64] = {0};
	for (int top = 0; top >= 0; --top) {
		const GumboNode *node = stack[top];
		int d = depth[top];
		if (node->type == GUMBO_NODE_ELEMENT) {
			printf("%*s<%s>\n", d * 2, "", gumbo_normalized_tagname(node->v.element.tag));
			for (unsigned i = node->v.element.children.length; i-- > 0; ++top) {
				stack[top] = node->v.element.children.data[i];
				depth[top] = d + 1;
			}
		} else if (node->type == GUMBO_NODE_TEXT) {
			printf("%*s%s\n", d * 2, "", node->v.text.text);
		}
	}

	gumbo_destroy_output(&kGumboDefaultOptions, output);
}
```
```
gcc example.c `pkg-config --cflags --libs gumbo`
```

A variety of sample programs can be found in the [examples](https://codeberg.org/gumbo-parser/gumbo-parser/src/branch/master/examples) directory.
To build them, enable the `examples` build option during setup, e.g.:
```
meson setup build -Dexamples=true
```

## Learning more

* Language bindings and other tools: [doc/bindings.md](https://codeberg.org/gumbo-parser/gumbo-parser/src/branch/master/doc/bindings.md)
* Contributing guide: [doc/contributing.md](https://codeberg.org/gumbo-parser/gumbo-parser/src/branch/master/doc/contributing.md)
* Debugging notes: [doc/debugging.md](https://codeberg.org/gumbo-parser/gumbo-parser/src/branch/master/doc/debugging.md)
* Maintenance directions: [doc/maintaining.md](https://codeberg.org/gumbo-parser/gumbo-parser/src/branch/master/doc/maintaining.md)
* Test suite guide: [doc/testing.md](https://codeberg.org/gumbo-parser/gumbo-parser/src/branch/master/doc/testing.md)

## Package availability

[![Packaging status](https://repology.org/badge/vertical-allrepos/gumbo-parser.svg?columns=4)](https://repology.org/project/gumbo-parser/versions)
