# Методи

Методите се обозначават подобно на функциите:

```rust,editable
struct Owner(i32);

impl Owner {
    // Отбелязваме живота на аргументите като при самостоятелна функция.
    fn add_one<'a>(&'a mut self) { self.0 += 1; }
    fn print<'a>(&'a self) {
        println!("`print`: {}", self.0);
    }
}

fn main() {
    let mut owner = Owner(18);

    owner.add_one();
    owner.print();
}
```

### Вижте също:

[Методи][methods]

[methods]: ../../fn/methods.md
