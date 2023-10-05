# Иерархия на файловете 

Модулите могат да бъдат организирани във файлова иерархия. Да разбием примера от раздел [Видимост][visibility] във файлове:

```shell
$ tree .
.
├── my
│   ├── inaccessible.rs
│   └── nested.rs
├── my.rs
└── split.rs
```

В `split.rs`:

```rust,ignore
// Това обявление ще търси файл с име `my.rs` и ще вмъкне неговото съдържание в
// модул с име `my` в този обхват
mod my;

fn function() {
    println!("извикахме `function()`");
}

fn main() {
    my::function();

    function();

    my::indirect_access();

    my::nested::function();
}

```

В `my.rs`:

```rust,ignore
// По подобен начин `mod inaccessible` и `mod nested` ще намерят файловете
// `nested.rs` и `inaccessible.rs` и ще ги вмъкнат тук в съответните им модули
mod inaccessible;
pub mod nested;

pub fn function() {
    println!("извикахме `my::function()`");
}

fn private_function() {
    println!("извикахме `my::private_function()`");
}

pub fn indirect_access() {
    print!("извикахме `my::indirect_access()`, that\n> ");

    private_function();
}
```

В `my/nested.rs`:

```rust,ignore
pub fn function() {
    println!("извикахме `my::nested::function()`");
}

#[allow(dead_code)]
fn private_function() {
    println!("извикахме `my::nested::private_function()`");
}
```

В `my/inaccessible.rs`:

```rust,ignore
#[allow(dead_code)]
pub fn public_function() {
    println!("извикахме `my::inaccessible::public_function()`");
}
```

Да проверим дали всичко работи както преди:

```shell
$ rustc split.rs && ./split
called `my::function()`
called `function()`
called `my::indirect_access()`, that
> called `my::private_function()`
called `my::nested::function()`
```

[visibility]: visibility.md
