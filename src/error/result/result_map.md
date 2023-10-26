# `map` за `Result`

Ако просто се паникьосваме в предния пример с `multiply`, то кодът ни е немощен.
Обикновено връщаме грешката към извикващия блок, за да може той да реши как
правилно да я обработи.

Първо трябва да знаем с какъв вид грешка си имаме работа. За да определим типа
вътре в `Err`, гледаме [`parse()`][parse], който е осъществен с отличителя
[`FromStr`][from_str] за типа [`i32`][i32]. Виждаме, че типът `Err` е указан
като [`ParseIntError`][parse_int_error].

В примера по-долу иначе ясното изявление `match` води като цяло до по-труден за
четене код.

```rust,editable
use std::num::ParseIntError;

// Сега сме променили типа за връщане. Получаваме го чрез съпоставяне по
// образец (`match`), без да ползваме `unwrap()`.
fn multiply(first_number_str: &str, second_number_str: &str)
   -> Result<i32, ParseIntError> {
    match first_number_str.parse::<i32>() {
        Ok(first_number)  => {
            match second_number_str.parse::<i32>() {
                Ok(second_number)  => {
                    Ok(first_number * second_number)
                },
                Err(e) => Err(e),
            }
        },
        Err(e) => Err(e),
    }
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(n)  => println!("n е {}", n),
        Err(e) => println!("Грешка: {}", e),
    }
}

fn main() {
    // Това е по-смислен отговор.
    let twenty = multiply("10", "2");
    print(twenty);

    // Следното съобщение за грешка помага много повече.
    let tt = multiply("t", "2");
    print(tt);
}
```

За щастие, `map`, `and_then` и много други съчетатели от `Option` са
осъществени също и за `Result`. Страницата с документацията на
[`Result`][result] съдържа пълния списък.

```rust,editable
use std::num::ParseIntError;

// Както с `Option`, и тук можем да ползваме съчетатели като `map()`.
// Тази функция, иначе същата като горе, се чете така:
// Умножаваме, ако и двете стойности могат да се извлекат от низа, иначе
// предаваме грешката нагоре.
fn multiply(first_number_str: &str, second_number_str: &str)
   -> Result<i32, ParseIntError> {
    first_number_str.parse::<i32>().and_then(|first_number| {
        second_number_str.parse::<i32>().map(|second_number|
            first_number * second_number)
    })
}

fn print(result: Result<i32, ParseIntError>) {
    match result {
        Ok(число)  => println!("числото е {}", число),
        Err(e) => println!("Грешка: {}", e),
    }
}

fn main() {
    // Това е по-смислен отговор.
    let twenty = multiply("10", "2");
    print(twenty);

    // Следното съобщение за грешка помага много повече.
    let tt = multiply("t", "2");
    print(tt);
}
```

[parse]: https://doc.rust-lang.org/std/primitive.str.html#method.parse
[from_str]: https://doc.rust-lang.org/std/str/trait.FromStr.html
[i32]: https://doc.rust-lang.org/std/primitive.i32.html
[parse_int_error]: https://doc.rust-lang.org/std/num/struct.ParseIntError.html
[result]: https://doc.rust-lang.org/std/result/enum.Result.html
