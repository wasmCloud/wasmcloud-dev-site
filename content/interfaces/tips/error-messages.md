---
title: "Error messages"
draft: false
---


Error messages from the smithy model parser, invoked by `build.rs` in Rust project, can be very long and detailed, sometimes more verbose than you need. 

Errors from `wash lint` or `wash validate` are usually more specific.

For build errors, look for the part of the message containing 

```
line_col: Pos[L,C]
```

The numbers L and C are the line number and column number of the syntax error. Shortly after that there should be a `line:` showing the line of the input file containing the error.


## Clippy warning on `&String` parameters

If your `.smithy` model has an operation whose input parameter is a 'String', clippy may generate the following warning:
```
warning: writing `&String` instead of `&str` involves a new object where a slice will do
```

That's incorrect for our use case (try changing it and you'll see that change `&String` to `&str` doesn't work). To avoid this warning, add `#![allow(clippy::ptr_arg)]` as the first line in your interface project's src/lib.rs (or whichever source file includes the generated code from OUT_DIR).
