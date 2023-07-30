# Тестове

Както знаем, тестването е неразделна част от всеки софтуер. Ръждьо има
първокласна поддръжка за  единични (unit) и интеграционни тестове. Вижте глава
11 от [„The Rust Programming
Language”](https://doc.rust-lang.org/book/ch11-00-testing.html).

Там е описано как се пишат тестове. Можем да сложим единичните тестове в
модулите, които тестват, а интеграционните в отделна директория `tests/`:

```txt
foo
├── Cargo.toml
├── src
│   └── main.rs
│   └── lib.rs
└── tests
    ├── my_test.rs
    └── my_other_test.rs
```

Всеки файл в `tests` е отделен [интеграционен
тест](https://doc.rust-lang.org/book/ch11-03-test-organization.html#integration-tests),
сиреч тест, който проверява библиотеката сякаш се ползва от зависещ от нея кош.

Глава [Тестване][testing] разглежда  трите вида тестване: 
[Единично][unit_testing], [Документално][doc_testing], и
[Интеграционно][integration_testing]. 

`cargo` предоставя лесен начин за изпълняване на всички тестове!

```shell
$ cargo test
```

Ще видите да се извежда следното на екрана:

```shell
$ cargo test
   Compiling blah v0.1.0 (file:///nobackup/blah)
    Finished dev [unoptimized + debuginfo] target(s) in 0.89 secs
     Running target/debug/deps/blah-d3b32b97275ec472

running 3 tests
test test_bar ... ok
test test_baz ... ok
test test_foo_bar ... ok
test test_foo ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

Също можете да изпълните само някои тестове, чиито имена частично съвпадат с
написаното от вас:

```shell
$ cargo test test_foo
```

```shell
$ cargo test test_foo
   Compiling blah v0.1.0 (file:///nobackup/blah)
    Finished dev [unoptimized + debuginfo] target(s) in 0.35 secs
     Running target/debug/deps/blah-d3b32b97275ec472

running 2 tests
test test_foo ... ok
test test_foo_bar ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 2 filtered out
```

Имайте следното предвид: Cargo може да изпълнява няколко теста едновременно,
така че уверете се, че те не си съперничат по някакъв начин.

Един пример как едновременността създава проблеми е когато два теста пишат в
един и същи файл, както по-долу:

```rust
#[cfg(test)]
mod tests {
    // Внасяме нужните модули
    use std::fs::OpenOptions;
    use std::io::Write;

    // Този тест пише във файл
    #[test]
    fn test_file() {
        // Отваря файла ferris.txt или създава нов, ако не съществува.
        let mut file = OpenOptions::new()
            .append(true)
            .create(true)
            .open("ferris.txt")
            .expect("Failed to open ferris.txt");

        // Print "Ferris" 5 times.
        for _ in 0..5 {
            file.write_all("Ferris\n".as_bytes())
                .expect("Could not write to ferris.txt");
        }
    }

    // Този тест се опитва да пише в същия файл
    #[test]
    fn test_file_also() {
        // Отваря файла ferris.txt или създава нов, ако не съществува.
        let mut file = OpenOptions::new()
            .append(true)
            .create(true)
            .open("ferris.txt")
            .expect("Failed to open ferris.txt");

        // Print "Corro" 5 times.
        for _ in 0..5 {
            file.write_all("Corro\n".as_bytes())
                .expect("Could not write to ferris.txt");
        }
    }
}
```

Макар намерението ни да е да получим следното:
```shell
$ cat ferris.txt
Ferris
Ferris
Ferris
Ferris
Ferris
Corro
Corro
Corro
Corro
Corro
```
Съдържанието на `ferris.txt` е това:
```shell
$ cargo test test_foo
Corro
Ferris
Corro
Ferris
Corro
Ferris
Corro
Ferris
Corro
Ferris
```

[testing]: ../testing.md
[unit_testing]: ../testing/unit_testing.md
[integration_testing]: ../testing/integration_testing.md
[doc_testing]: ../testing/doc_testing.md
