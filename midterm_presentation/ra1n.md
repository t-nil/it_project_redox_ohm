---
layout: intro 
---


# C standard library (`libc`)

- Provides basic functions, types, macros
- Handles platform-specific implementation details 
  - *so there's work to be done on a new platform*
- There is an ISO standard
- There are multiple implementations (glibc, llvm-libc, musl, …)

E.g. `<stdio.h>, <stdlib.h>, <string.h>, <math.h>, <threads.h>`

---

# `relibc`: C standard library for RedoxOS (and Linux), written in Rust

- Compatibility layer for C software (there are many)
- Written in Rust => C bindings generated automatically w/ `cbindgen`
  - Rust gets you type+memory safety => **best of both worlds**
- Maps operations to syscalls - difference between Redox and Linux

---

# TODO Syscalls

---

# TODO syscall example

```rust
pub unsafe extern "C" fn fclose(stream: *mut FILE) -> c_int {
    let stream = &mut *stream;
    flockfile(stream);

    let mut r = stream.flush().is_err();
    let close = Sys::close(*stream.file) < 0;
    r = r || close;

    if stream.flags & constants::F_PERM == 0 {
        // Not one of stdin, stdout or stderr
        let mut stream = Box::from_raw(stream);
        // Reference files aren't closed on drop, so pretend to be a reference
        stream.file.reference = true;
    } else {
        funlockfile(stream);
    }

    r as c_int
}
```

```rust
[...]
    let close = Sys::close(*stream.file) < 0;
[...]
```

---

# TODO syscall example



```rust
/// Close a file
pub fn close(fd: usize) -> Result<usize> {
    unsafe { syscall1(SYS_CLOSE, fd) }
}
```

---

# TODO `cbindgen`

---

# TODO example for `cbindgen` generation

---

# TODO `relibc`: tests

---

# TODO cbindgen

