# Прякори за `Result`

Какво да правим, когато искаме да използваме определен тип `Result`
многократно? Да си припомним, че Ръждьо ни позволява да създаваме
[прякори][typealias]. За наше удобство можем да опишем прякор за точно
определен `Result`.

Може да се окаже особено полезно да си създадем прякори за грешки само за един
модул. Грешките в даден модул често имат един и същи тип `Err`. Така че с един
прякор можем сбито да опишем _всички_ подобни типове `Result`. Това е толкова
полезно, че стандартната библиотека `std` дори предоставя такъв тип:
[`io::Result`][io_result]!

Ето бърз пример, за да покажем как се пише:

```rust,editable
use std::num::ParseIntError;

// Описваме обобщен прякор за `Result` с тип за грешка `ParseIntError`.
type AliasedResult<T> = Result<T, ParseIntError>;

// Използваме горния прякор за обръщение към нашия особен тип `Result`.
fn multiply(first_number_str: &str, second_number_str: &str)
   -> AliasedResult<i32> {
    first_number_str.parse::<i32>().and_then(|first_number| {
        second_number_str.parse::<i32>().map(|second_number|
            first_number * second_number)
    })
}

// Тук прякорът отново ни позволява да спестим малко място.
fn print(result: AliasedResult<i32>) {
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

### Вижте също:

[`io::Result`][io_result]

[typealias]: ../../types/alias.md
[io_result]: https://doc.rust-lang.org/std/io/type.Result.html
