# Rust C-ABI Library Template

A minimal template for building a Rust library and calling it from C-ABI
langauges including C, JavaScript (Node.js), Lua, and Python.

The minimal library, [src/lib.rs](src/lib.rs), includes a landing pad to catch
panics in the Rust code and prevent them from cross the FFI boundary. The main
function ``rust_function()`` expects to receive a C string as an argument, and
returns a pointer to a C-formatted struct containing a result code and a C
string message or payload:

```rust
#[repr(C)]
pub struct CallResult {
   status: i32,       // 0: success,      1: failure,         2: panic caught
   text: *mut c_char  // success message, failure message, or panic message
}
```

The landing pad will return whatever its protected code returns, unless a
panic occurs, in which case, it returns a ``status`` of 2 and a ``text``
message describing the panic.

Since the ``CallResult`` struct is allocated by Rust, it must be freed by
Rust, so the calling application must call ``free_result()`` to release the
memory.

Also included is a Travis-CI configuration for building a Rust library as part
of a project written in another language (in this case, Python). At present,
the [Travis Rust documentation](https://docs.travis-ci.com/user/languages/rust/)
describes how to use their service for projects that are primarily Rust-based
('language: rust'), but not how to add Rust components to an existing (e.g.
'language: python') project.

## C

No makefile is included for the ``c_test.c`` test application, but it can be
built and run somewhat like this:

```sh
$ cargo build
$ gcc -Wall -O2 -o c_test c_test.c -L target/debug -lrust_c_abi_library_template
$ LD_LIBRARY_PATH=target/debug ./ffi_test
```

## Lua

The ``lua_test.lua`` test application was tested using Lua 5.3 with
Pierre Chapuis's fork of [luaffi](https://github.com/catwell/luaffi), which
adds Lua 5.3 to James McKaskill's (unmaintained?) [version](https://github.com/jmckaskill/luaffi).
Either McKaskill's version or the [FaceBook
fork](https://github.com/facebook/luaffifb) will probably work with older
versions of Lua.

Example:

```sh
$ # install luaffi using your favorite method
$ cargo build
$ lua lua_test.lua
```

## Node.js

The ``node_test.js`` test application was tested using Node 6.9.1 and the
[ffi](https://www.npmjs.com/package/ffi),
[ref](https://www.npmjs.com/package/ref), and
[ref-struct](https://www.npmjs.com/package/ref-struct) packages.

Example:

```sh
$ cargo build
$ npm install
$ node node_test.js
```

## License

rust_c-abi_library_template is copyright © 2017 Sean Bolton, and licensed
under the MIT license:

    Copyright (c) 2017 Sean Bolton

    Permission is hereby granted, free of charge, to any person obtaining
    a copy of this software and associated documentation files (the
    "Software"), to deal in the Software without restriction, including
    without limitation the rights to use, copy, modify, merge, publish,
    distribute, sublicense, and/or sell copies of the Software, and to
    permit persons to whom the Software is furnished to do so, subject to
    the following conditions:

    The above copyright notice and this permission notice shall be
    included in all copies or substantial portions of the Software.

    THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
    EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
    MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
    NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
    LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
    OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
    WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
