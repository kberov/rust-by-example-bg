# Собствени условия

Някои условия като `target_os` се предоствят от `rustc`, но вашите собствени
условия задължително се подават на `rustc` чрез флага `--cfg.

```rust,editable,ignore,mdbook-runnable
#[cfg(some_condition)]
fn conditional_function() {
    println!("condition met!");
}

fn main() {
    conditional_function();
}
```

Изпълнете долната команда и вижте какво се случва ако не подадете флага със
съответната стойност.

С потрбителски флаг `cfg`:

```shell
$ rustc --cfg some_condition custom.rs && ./custom
condition met!
```
