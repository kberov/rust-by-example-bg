# `Result`

[`Result`][result] е по-богата разновидност на типа [`Option`][option], която
описва възможна *грешка* вместо възможна *липса* на стойност.

Сиреч, `Result<T, E>` може да има една от следните стойности:

* `Ok(T)`: Намерен е член от тип `T`
* `Err(E)`: Открита е грешка, чиято стойност е от тип `E`

Обичайно, очакваният изход е `Ok`, а неочакваният изход е `Err`.

Както `Option`, `Result` има много придадени методи. `unwrap()` например, или
произвежда член от тип `T`, или завършва програмата с `panic`. За обработка на
различни случаи `Result` има много припокриващи се с `Option` съчетатели.

Докато работите с Ръждьо, вероятно ще се натъквате на методи, които връщат типа
`Result`. Такъв е методът [`parse()`][parse]. Не винаги е възможно да се
_извлече_[^parse] целевия тип при разбора на низ. Затова `parse()` връща
`Result` и така показва, че е възможен неуспех.

Нека видим какво се случва, когато успешно и неуспешно правим разбор на низ
(`parse()`):

```rust,editable,ignore,mdbook-runnable
fn multiply(first_number_str: &str, second_number_str: &str) -> i32 {
    // Нека пробваме да извлечем числото с `unwrap()`. Дали ще ни ухапе?
    let first_number = first_number_str.parse::<i32>().unwrap();
    let second_number: i32 = second_number_str.parse().unwrap();
    first_number * second_number
}

fn main() {
    let twenty = multiply("10", "2");
    println!("double is {}", twenty);

    let tt = multiply("t", "2");
    println!("double is {}", tt);
}
```

При неуспешния случай `parse()` връща грешка, така че  `unwrap()` да се
паникьоса. Освен това `panic` прекратява програмата ни и показва неприятно
съобщение за грешка.

За да подобрим качеството на нашето съобщение за грешка, трябва да уточним
какъв тип ще връщаме и да помислим как ще обработваме грешката. 

## Използване на `Result` в `main`

Типа `Result` може да се връща също и от функцията `main`, ако укажем това
изрично. Обичайно `main` има следния вид:

```rust
fn main() {
    println!("Hello World!");
}
```

Обаче `main` може да връща и `Result`. Ако се случи грешка във функцията
`main`, тя ще върне код за грешка и ще отпечати подсказващо представяне на
грешката (с помощта на отличителя [`Debug`]). Следният пример ни показва такъв
развой и загатва за неща, които разглеждаме в следващия раздел.

```rust,editable
use std::num::ParseIntError;

fn main() -> Result<(), ParseIntError> {
    let number_str = "10";
    let number = match number_str.parse::<i32>() {
        Ok(number)  => number,
        Err(e) => return Err(e),
    };
    println!("{}", number);
    Ok(())
}
```
## Б. пр.

[^parse]: раз-бир-ам, извличам, разбивам на *парче*-та – parse

[option]: https://doc.rust-lang.org/std/option/enum.Option.html
[result]: https://doc.rust-lang.org/std/result/enum.Result.html
[parse]: https://doc.rust-lang.org/std/primitive.str.html#method.parse
[`Debug`]: https://doc.rust-lang.org/std/fmt/trait.Debug.html
[the following section]: result/early_returns.md
