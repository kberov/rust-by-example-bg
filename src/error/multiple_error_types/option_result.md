# Извличане на `Result` от `Option`

Най-лесният начин за справяне със смесени типове за грешки е като просто ги
вгнездим един в друг.

```rust,editable
use std::num::ParseIntError;

fn double_first(vec: Vec<&str>) -> Option<Result<i32, ParseIntError>> {
    vec.first().map(|first| {
        first.parse::<i32>().map(|n| 2 * n)
    })
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    println!("Първото удвоено е {:?}", double_first(numbers));

    println!("Първото удвоено е {:?}", double_first(empty));
    // Грешка 1: входният вектор е празен

    println!("Първото удвоено е {:?}", double_first(strings));
    // Грешка 2: първата стойност не може да бъде сведена до число
}
```

В някои случаи ще искаме да спрем изпълнението при грешка (като при
[`?`][enter_question_mark]), но да продължим, когато изборът `Option` е `None`.
Няколко съчетателя може да ни дойдат отръки, ако искаме да сменим местата на
`Result` и `Option`.

```rust,editable
use std::num::ParseIntError;

fn double_first(vec: Vec<&str>) -> Result<Option<i32>, ParseIntError> {
    let opt = vec.first().map(|first| {
        first.parse::<i32>().map(|n| 2 * n)
    });

    opt.map_or(Ok(None), |r| r.map(Some))
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    println!("Първото удвоено е {:?}", double_first(numbers));
    println!("Първото удвоено е {:?}", double_first(empty));
    println!("Първото удвоено е {:?}", double_first(strings));
}
```

[enter_question_mark]: ../result/enter_question_mark.md
