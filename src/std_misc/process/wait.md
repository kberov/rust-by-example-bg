# Изчакване

Ако искате да изчакате някой `process::Child` да приключи, трябва да извикате
`Child::wait`. Той ще върне обект от тип `process::ExitStatus`.

```rust,ignore
use std::process::Command;

fn main() {
    let mut child = Command::new("sleep").arg("5").spawn().unwrap();
    let _result = child.wait().unwrap();

    println!("Достигнахме края на функцията main.");
}
```

```bash
$ rustc wait.rs && ./wait
# `wait` кара програмата ни да работи пет секунди, докато командата `sleep 5`
# не приключи
Достигнахме края на функцията main.
```
