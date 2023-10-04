# Crates

Атрибутът `crate_type` казва на компилатора дали един кош е изпълним файл или
библиотека, и дори какъв тип библиотека. А атрибутът `crate_name` задава името
на коша.

Важно е да отбележим обаче, че и двата атрибута **не влияят на компилирането**,
когато използваме Cargo. И понеже Cargo се ползва в повечето проекти на Ръждьо,
това означава, че тези атрибути рядко се срещат в истински проекти.

В lib.rs

```rust,editable
// Този кош е библиотека
#![crate_type = "lib"]
// Библиотеката се казва "rary"
#![crate_name = "rary"]

pub fn public_function() {
    println!("извикахме rary's `public_function()`");
}

fn private_function() {
    println!("извикахме rary's `private_function()`");
}

pub fn indirect_access() {
    print!("извикахме rary's `indirect_access()`, that\n> ");

    private_function();
}
```

Когато ползваме атрибута `crate_type`, вече няма нужда да подаваме флага
`--crate-type` на `rustc`.

```shell
$ rustc lib.rs
$ ls lib*
library.rlib
```
