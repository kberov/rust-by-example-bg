# Създаване на библиотека 

Да създадем библиотека и после да видим как да я свържем с друг кош.

В `rary.rs`:

```rust,ignore
pub fn public_function() {
    println!("called rary's `public_function()`");
}

fn private_function() {
    println!("called rary's `private_function()`");
}

pub fn indirect_access() {
    print!("called rary's `indirect_access()`, that\n> ");

    private_function();
}
```

```shell
$ rustc --crate-type=lib rary.rs
$ ls lib*
library.rlib
```
Библиотеките получават представка "lib" пред името и по подразбиране се
именуват на главния файл за коша. Това име може да бъде променено, като се
подаде на `rustc` флага `--crate-name` или като се използва атрибута
[`crate_name`][crate-name].

[crate-name]: ../attribute/crate.md
