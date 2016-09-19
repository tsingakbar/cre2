This is a fork from <http://github.com/marcomaggi/cre2/>.

with the following changes
==========================
* use cmake to repace autoconf, will only generate a static lib.
* add shortcut codes in `DEFINE_MATCH_REX_FUN` when there're no needs to return any matches to
speed up my own usage.
* includes go wrappers adapted from <https://github.com/wordijp/golang-re2>, 
which patched the cre2's header for better cgo integration.
* modify the go wrapper to compile with newer Go compiler(see the notes below), and also some
modification to accommodate the previous optimization in C binding's `DEFINE_MATCH_REX_FUN`.

to use the C binding
====================
1. compile the re2's static lib, use `cmake -DRE2_LIB_DIR=/re2dir/obj -DRE2_HEADER_DIR=/re2dir .`
to geneate the Makefile.
2. run `make` to get the `libcre2.a`.

```cpp
bool cre2_demo(std::string regstr, std::string textstr) {
    bool ret = false;
    auto opt = cre2_opt_new();
    cre2_opt_set_log_errors(opt, 0);
    cre2_opt_set_max_mem(opt, 1024*1024*10);
    auto regexp = cre2_new(regstr.c_str(), regstr.length(), opt);
    const cre2_string_t text = {textstr.c_str(), int(textstr.length())};
    auto start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < 100000; ++i) {
      ret = cre2_partial_match_re(regexp, &text, NULL, 0);
    }
    auto finish = std::chrono::high_resolution_clock::now();
    std::cout << std::chrono::duration_cast<std::chrono::nanoseconds>(finish-start).count() << "ns\n";
    cre2_delete(regexp);
    cre2_opt_delete(opt);
    return ret;
}
```

to use the go binding
=====================
1. make sure the C binding is properly compiled.
2. copy or soft link `libre2.a` and `libstdc++.a` besides c static binding `libcre2.a`.
3. `go get -v github.com/tsingakbar/cre2`
4. import this package and use it in your code. currently the go wrapper is 1.5-2.0x
slower than the C bindings in my tests. but it is still much more faster than golang's
regexp stdlib package which claims it is implemented with the same algorithm as RE2,
but completely written in golang.
```go
var (
	regexpFilter *re2.Regexp
	closer       *re2.Closer
  err error
)
if regexpFilter, closer, err = re2.Compile(regexpStr); err != nil {
	panic(err)
}
var result bool
var bEpoch = time.Now()
for i := 0; i < 100000; i++ {
	result = regexpFilter.Match(textbytes)
}
fmt.Printf("cre2 %v\n", time.Since(bEpoch))
fmt.Println(result)
closer.Close(regexpFilter)
```

NOTE: Since Go 1.6, you can no longer create a C struct like `cre2_string_t` in Go memory while
setting one of its field to another pointer in Go memory like setting `cre2_string_t::data` to
a slice's pointer.
To solve this problem, you have to write wrappers to alloc these kind of C struct in C memory
(stack space is prefered).
By now I have only covered `cre2_partial_match_re()` to fullfill my own needs. You probably need
to implement the others in `re2.go`, so any pull requests is welcomed.

