# Описване на тип за грешка 

Понякога кодът може да бъде опростен, като се маскират всички различни грешки
с единен тип. Щем показа това с потребителска грешка.

Ръждьо ни позволява да описваме собствени типове за грешки. Ето общите
изисквания за „добър” тип грешка:

* Представя различни грешки чрез еднакъв тип;
* Показва приятни на вид съобщения за грешка;
* Лесен е за сравняване с други типове:
    - Добре: `Err(EmptyVec)`;
    - Зле: `Err("Please use a vector with at least one element".to_owned())`.
* Може да съдържа сведения за грешката:
    - Добре: `Err(BadChar(c, position))`;
    - Зле: `Err("+ cannot be used here".to_owned())`.
* Подхожда си с другите грешки.

```rust,editable
use std::fmt;

type Result<T> = std::result::Result<T, DoubleError>;

// Описваме наши типове за грешки. Можем да ги нагодим за начина, по който
// обработваме грешките. Сега ще можем да си пишем наши грешки, да отпращаме към
// някакво вътрешно осъществление за грешка, или нещо между двете.
#[derive(Debug, Clone)]
struct DoubleError;

// Хвърлянето на грешка е напълно отделно от това как се показва.
// Не се притеснявайте, ако имате сложна логика в начина на показване.
//
// Забележете, че не съхраняваме никакви допълнителни сведения за грешките.
// Това означава, че не можем да потвърдим точно кой низ не може да бъде разбран,
// без да променим нашите типове, така че да носят тези данни със себе си.
impl fmt::Display for DoubleError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

fn double_first(vec: Vec<&str>) -> Result<i32> {
    vec.first()
        // Променяме грешката да бъде от нашия нов тип.
        .ok_or(DoubleError)
        .and_then(|s| {
            s.parse::<i32>()
                // Тук също ползваме новия тип.
                .map_err(|_| DoubleError)
                .map(|i| 2 * i)
        })
}

fn print(result: Result<i32>) {
    match result {
        Ok(n) => println!("The first doubled is {}", n),
        Err(e) => println!("Error: {}", e),
    }
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    print(double_first(numbers));
    print(double_first(empty));
    print(double_first(strings));
}
```
