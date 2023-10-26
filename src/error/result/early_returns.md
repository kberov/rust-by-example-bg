# Ранни връщания

В предишния пример обработвахме грешките изрично с помощта на съчетатели. Друг
начин да се справяме с подобни случаи е да ползваме съчетание от изявления
`match` и *ранни връщания*.

Сиреч, можем просто да спрем изпълнението на функцията и да върнем грешката,
ако има такава. За някои от нас този начин на писане може да се окаже по-лесен
както за писане, така и за четене. Да разгледаме тази разновидност на предния
пример, пренаписан с ранни връщания.

```rust,editable
use std::num::ParseIntError;

fn multiply(first_number_str: &str, second_number_str: &str) -> Result<i32, ParseIntError> {
    let first_number = match first_number_str.parse::<i32>() {
        Ok(first_number)  => first_number,
        Err(e) => return Err(e),
    };

    let second_number = match second_number_str.parse::<i32>() {
        Ok(second_number)  => second_number,
        Err(e) => return Err(e),
    };

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

Дотук се научихме да обработваме грешките изрично с помощта на съчетатели и
ранни връщания. Въпреки че обикновено искаме да избегнем паникьосване,
изричната обработка на грешки си е трудоемка.

В следващия раздел ще се запознаем със `?` за случаите, в които просто искаме да
разгърнем стойността (`unwrap`), но без да предизвикаме `panic`.
