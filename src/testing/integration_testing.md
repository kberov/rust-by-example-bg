# Тестове при внедряване

[Поединичните тестове][unit] тестват всеки модул поотделно. Те са малки и могат
да тестват частен код. Тестовете при внедряване са външни за коша ви и ползват
само общодостъпната му външност по същия начин, както и всеки друг код.
Тяхното предназначение е да проверят дали различните части на библиотеката ви
работят правилно заедно.

Cargo гледа за тестове при внедряване в директорията `tests` до `src`.

Файл `src/lib.rs`:

```rust,ignore
// Опишете това в кош с име `adder`.
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}
```

Файл с тест: `tests/integration_test.rs`:

```rust,ignore
#[test]
fn test_add() {
    assert_eq!(adder::add(3, 2), 5);
}
```

Пускаме тестовете с командата `cargo test`:

```shell
$ cargo test
running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target/debug/deps/integration_test-bcd60824f5fbfe19

running 1 test
test test_add ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests adder

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

Всеки файл с изходен код на Ръждьо в директорията `tests` бива компилиран като
отделен кош. За да ползваме общ код в различните тестове, можем да направим
модул с общодостъпни функции, да го внесем и ползваме в тестовете.

Файл `tests/common/mod.rs`:

```rust,ignore
pub fn setup() {
    // Някакъв подготвителен код за създаване на файлове, директории, пускане на
    // сървъри и т.н.
}
```

Файл с тест: `tests/integration_test.rs`

```rust,ignore
// Внасяме общия модул.
mod common;

#[test]
fn test_add() {
    // Ползваме общия код.
    common::setup();
    assert_eq!(adder::add(3, 2), 5);
}
```

Също ще работи и ако създадем модула като `tests/common.rs`, но не се препоръчва,
защото пускът на тестове смята файла за тестов кош и ще се опита да
пусне тестове в него.

[unit]: unit_testing.md
[mod]: ../mod.md
