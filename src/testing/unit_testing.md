# Поединично тестване

Тестовете в Ръждьо са функции, които проверяват дали друг – нетестов код
действа по очаквания начин. Телата на тестовите функции обикновено извършват
някакви първоначални настройки, изпълняват кода, който искаме да тестваме и
след това проверяват дали изхода от изпълнението на кода е каквото очакваме.

Повечето единични тестове се пишат в отделен [модул][mod] с име `tests` и
[атрибут][attribute] `#[cfg(test)]`. Тестовите функции се обозначават с атрибута
`#[test]`.

Тестовете се провалят, когато нещо в тестова функция се [паникьоса][panic]. Има
някои помощни [макроси][macros]:

* `assert!(израз)`[^assert] – паникьосва се, ако изразът се оцени като `false`.
* `assert_eq!(ляво, дясно)` и `assert_ne!(ляво, дясно)` – проверяват се левият
  и десният израз за еднаквост и нееднаквост съответно.

```rust,ignore
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

// Това е наистина калпава функция за събиране. Нейното предназначение в този
// пример е да се провали.
#[allow(dead_code)]
fn bad_add(a: i32, b: i32) -> i32 {
    a - b
}

#[cfg(test)]
mod tests {
    // Забележете този полезен устойчив израз. Така внасяме имена отвън в обхвата на
    // `mod tests`.
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(1, 2), 3);
    }

    #[test]
    fn test_bad_add() {
        // Когато това потвърждение се изпълни, тестът ще се провали.
        // Забележете, че частните функции също могат да бъдат тествани!
        assert_eq!(bad_add(1, 2), 3);
    }
}
```

Тестовете могат да се изпълняват с `cargo test`.

```shell
$ cargo test

running 2 tests
test tests::test_bad_add ... FAILED
test tests::test_add ... ok

failures:

---- tests::test_bad_add stdout ----
        thread 'tests::test_bad_add' panicked at 'assertion failed: `(left == right)`
  left: `-1`,
 right: `3`', src/lib.rs:21:8
note: Run with `RUST_BACKTRACE=1` for a backtrace.


failures:
    tests::test_bad_add

test result: FAILED. 1 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out
```

## Тестове и `?`
Нито един от предишните единични тестове нямаше тип за връщане. Но в Ръждьо
2018, тестовете могат да връщат `Result<()>`, което ви позволява да ползвате
`?` в тях! Това може да ги направи много по-кратки.

```rust,editable
fn sqrt(number: f64) -> Result<f64, String> {
    if number >= 0.0 {
        Ok(number.powf(0.5))
    } else {
        Err("negative floats don't have square roots".to_owned())
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_sqrt() -> Result<(), String> {
        let x = 4.0;
        assert_eq!(sqrt(x)?.powf(2.0), x);
        Ok(())
    }
}
```

Вижте [„Ръководството за изданията”][editionguide] за повече подробности.

## Тестване на паника

За да тествате функции, които може да се паникьосат, използвайте
атрибута`#[should_panic]`. Този атрибут приема изборен параметър `expected =`
с текста на съобщението при паника. Ако вашата функция се паникьосва по
различни начини, трябва да се уверите, че тествате правилната паника.
    
```rust,ignore
pub fn divide_non_zero_result(a: u32, b: u32) -> u32 {
    if b == 0 {
        panic!("Divide-by-zero error");
    } else if a < b {
        panic!("Divide result is zero");
    }
    a / b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_divide() {
        assert_eq!(divide_non_zero_result(10, 2), 5);
    }

    #[test]
    #[should_panic]
    fn test_any_panic() {
        divide_non_zero_result(1, 0);
    }

    #[test]
    #[should_panic(expected = "Divide result is zero")]
    fn test_specific_panic() {
        divide_non_zero_result(1, 10);
    }
}
```

Изпълнението на тези тестове ни дава:

```shell
$ cargo test

running 3 tests
test tests::test_any_panic ... ok
test tests::test_divide ... ok
test tests::test_specific_panic ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests tmp-test-should-panic

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

## Изпълняване на отделни тестове

За да изпълните точно определени тестове, можете да укажете името на теста при
изпълнение на командата `cargo test`.

```shell
$ cargo test test_any_panic
running 1 test
test tests::test_any_panic ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 2 filtered out

   Doc-tests tmp-test-should-panic

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

За да изпълните множество тестове, можете да укажете част от име на тест,
което съответства на всички тестове, които искате да изпълните.

```shell
$ cargo test panic
running 2 tests
test tests::test_any_panic ... ok
test tests::test_specific_panic ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 1 filtered out

   Doc-tests tmp-test-should-panic

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

## Пропускане[^ignore] на тестове

Тестовете могат да бъдат отбелязани с атрибута `#[ignore]`, за да ги изключите.
Или пък да ги изпълните с командата `cargo test -- --ignored`.

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(2, 2), 4);
    }

    #[test]
    fn test_add_hundred() {
        assert_eq!(add(100, 2), 102);
        assert_eq!(add(2, 100), 102);
    }

    #[test]
    #[ignore]
    fn ignored_test() {
        assert_eq!(add(0, 0), 0);
    }
}
```

```shell
$ cargo test
running 3 tests
test tests::ignored_test ... ignored
test tests::test_add ... ok
test tests::test_add_hundred ... ok

test result: ok. 2 passed; 0 failed; 1 ignored; 0 measured; 0 filtered out

   Doc-tests tmp-ignore

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

$ cargo test -- --ignored
running 1 test
test tests::ignored_test ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests tmp-ignore

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```
## Б.пр.

[^assert]: потвърждавам – assert

[^ignore]: пренебрегвам – ignore. В случая – „пропускам”

[^idiom]: устойчив израз, идиом – idiom


[attribute]: ../attribute.md
[panic]: ../std/panic.md
[macros]: ../macros.md
[mod]: ../mod.md
[editionguide]: https://doc.rust-lang.org/edition-guide/rust-2018/error-handling-and-panics/question-mark-in-main-and-tests.html
