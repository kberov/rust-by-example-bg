# Дъщерни процеси 

Структурата `process::Output` представлява изходът от завършил дъщерен процес,
а структурата `process::Command` е създател на процеси.

```rust,editable,ignore
use std::process::Command;

fn main() {
    let output = Command::new("rustc")
        .arg("--version")
        .output().unwrap_or_else(|e| {
            panic!("Неуспех при изпълнение на процес: {}", e)
    });

    if output.status.success() {
        let s = String::from_utf8_lossy(&output.stdout);

        print!("rustc успя, а стандартният изход беше:\n{}", s);
    } else {
        let s = String::from_utf8_lossy(&output.stderr);

        print!("rustc не успя а стандартната грешка беше:\n{}", s);
    }
}
```

(Пробвайте горния пример, като подадете неправилен флаг на `rustc`)
