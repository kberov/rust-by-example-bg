# Запознаване със `?`

Понякога ни се иска да имаме простотата на `unwrap` без вероятна `panic`-а.
Досега `unwrap` ни принуждаваше да влизаме все по-надълбоко, а на нас просто ни
трябваше стойността. Точно това е предназначението на `?`.

При откриването на грешка (`Err`) има две възможни действия:

1. `panic!`, което вече решихме, че е добре да се избягва по възможност;
2. `return`, защото `Err` означава, че не можем да обработим това състояние.

`?` е *почти*[^†] точно съответствиe на `unwrap`, което при неуспех връща `Err`,
 вместо да извиква `panic`. Да видим как можем да опростим предния случай, където
ползвахме съчетатели:

```rust,editable
use std::num::ParseIntError;

fn multiply(first_number_str: &str, second_number_str: &str)
   -> Result<i32, ParseIntError> {
    let first_number = first_number_str.parse::<i32>()?;
    let second_number = second_number_str.parse::<i32>()?;

    Ok(first_number * second_number)
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```

## Макросът `try!`

Допреди да имаше `?`, същото постигахме с макроса `try!`.
Сега се препоръчва операторът `?`, но все още може да се натъкнете на `try!`,
ако гледате по-стар код. Същата функция за умножение, `multiply` от предния
пример, ще изглежда така с `try!`:

```rust,editable,edition2015
// За да компилирате и пуснете примера без грешки, докато ползвате Cargo,
// променете стойността на полето издание (`edition`) в раздела `[package]`
// на файла `Cargo.toml` да бъде "2015".
// За да компилиратe направо с rustc:
// rustc ./src/bin/error_result_try.rs  --edition 2015\
//  -o target/debug/error_result_try
// За да изпълните:
// target/debug/error_result_try


use std::num::ParseIntError;

fn multiply(first_number_str: &str, second_number_str: &str)
   -> Result<i32, ParseIntError> {
    let first_number = try!(first_number_str.parse::<i32>());
    let second_number = try!(second_number_str.parse::<i32>());

    Ok(first_number * second_number)
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    print(multiply("10", "2"));
    print(multiply("t", "2"));
}
```


[^†]: въпросителен знак (?) – почти като `unwrap`. Вижте [re-enter ?][re_enter_?] за още подробности.

[re_enter_?]: ../multiple_error_types/reenter_question_mark.md
