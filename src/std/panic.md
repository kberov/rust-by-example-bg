# `panic!`

Макросът `panic!` може да се ползва за създаване на паника и да започне да
размотава стълпа си. Докато се размотава стълпът, средата за изпълнение[^runtime]
се грижи да освободи всички данни, които са _във владение_ на нишката, като
извиква унищожителите на всичките ѝ обекти.

Понеже работим само с еднонишкови програми, `panic!` предизвиква програмата да
покаже паническото съобщение и да приключи.

```rust,editable,ignore,mdbook-runnable
// Ново осъществяване на целочислено деление – (/)
fn division(dividend: i32, divisor: i32) -> i32 {
    if divisor == 0 {
        // Деление на нула предизвиква паника
        panic!("деление на нула");
    } else {
        dividend / divisor
    }
}

// The `main` task
fn main() {
    // Цяло число, поместено в купа̀
    let _x = Box::new(0i32);

    // Това действие ще доведе до провал на задачата.
    division(3, 0);

    println!("Няма да стигнем дотук!");

    // `_x` би трябвало да се унищожи тук.
}
```

Нека видим, че `panic!` не създава утечки в паметта.

<!-- REUSE-IgnoreStart -->
<!-- Prevent REUSE from parsing the copyright изявлениe in the sample code -->
```shell
$ rustc panic.rs && valgrind ./panic
==4401== Memcheck, a memory error detector
==4401== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.
==4401== Using Valgrind-3.10.0.SVN and LibVEX; rerun with -h for copyright info
==4401== Command: ./panic
==4401== 
thread '<main>' panicked at 'division by zero', panic.rs:5
==4401== 
==4401== HEAP SUMMARY:
==4401==     in use at exit: 0 bytes in 0 blocks
==4401==   total heap usage: 18 allocs, 18 frees, 1,648 bytes allocated
==4401== 
==4401== All heap blocks were freed -- no leaks are possible
==4401== 
==4401== For counts of detected and suppressed errors, rerun with: -v
==4401== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```
<!-- REUSE-IgnoreEnd -->

## Б. пр.

[^runtime]: средата за изпълнение – the runtime
