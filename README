
			   C wrapper for re2
			   =================

		  
This is a fork from <http://github.com/marcomaggi/cre2/>.

with the following changes
==========================

1. use cmake to repace autoconf, will only generate a static lib.
2. includes go wrappers adapted from <https://github.com/wordijp/golang-re2>, 
which patched the cre2's header.

To use the c binding
====================
1. compile the re2's static lib, use `cmake -DRE2_LIB_DIR=/re2dir/obj -DRE2_HEADER_DIR=/re2dir .` to geneate the Makefile.
2. run `make` to get the `libcre2.a`.

To use the go binding
=====================
1. make sure the c binding is properly compiled.
2. copy or soft link `libre2.a` and `libstdc++.a` besides c static binding `libcre2.a`.
3. `go get -v github.com/tsingakbar/cre2`
4. import this package and use it in your code.

NOTE: Since Go 1.6, you can no longer create a C struct like `cre2_string_t` in Go memory while set one of its field
to another pointer in Go memory like setting `cre2_string_t::data` to a slice's pointer.
To solve this problem, you have to write wrappers to alloc these kind of C struct in C memory(stack space is prefered).
By now I have only covered cre2_partial_match_re() to fullfill my own need, you probably need to implement others in
`re2.go`, so any pull request is welcomed.

