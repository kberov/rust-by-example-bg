# Обгръщане на грешки

Вместо да слагаме грешките в кутии, можем да ги обгръщаме в наш тип за грешка.

```rust,editable
use std::error;
use std::error::Error;
use std::num::ParseIntError;
use std::fmt;

type Result<T> = std::result::Result<T, DoubleError>;

#[derive(Debug)]
enum DoubleError {
    EmptyVec,
    // Ще оставим грешката за извличане на цяло число да си се обработва от
    // първоначалния отличител. Допълнителните сведения за грешката изискват да
    // добавим повече данни към типа.
    Parse(ParseIntError),
}

impl fmt::Display for DoubleError {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match *self {
            DoubleError::EmptyVec =>
                write!(f, "Моля, ползвайте вектор с поне един член."),
            // Обгърнатата грешка съдържа допълнителни сведения, а те са
            // достъпни чрез метода source().
            DoubleError::Parse(..) =>
                write!(f,
                    "Предоставеният низ не може да се обработи като цяло число."),
        }
    }
}

impl error::Error for DoubleError {
    fn source(&self) -> Option<&(dyn error::Error + 'static)> {
        match *self {
            DoubleError::EmptyVec => None,
            // Причината е начинът, по който е осъществен типа за грешка. Грешката
            // е сведена тихомълком до обекта на отличителя `&error::Error`.
            // Това работи, защото същинският тип осъществява отличителя `Error`.
            DoubleError::Parse(ref e) => Some(e),
        }
    }
}


// Осъществяваме превръщането от `ParseIntError` в `DoubleError`.
// Това въплъщение ще бъде извикано автоматично от `?` ако някоя грешка от тип 
// `ParseIntError` трябва да бъде превърната в `DoubleError`.
impl From<ParseIntError> for DoubleError {
    fn from(err: ParseIntError) -> DoubleError {
        DoubleError::Parse(err)
    }
}

fn double_first(vec: Vec<&str>) -> Result<i32> {
    let first = vec.first().ok_or(DoubleError::EmptyVec)?;
    // Тук ползваме въплъщението на `ParseIntError` от `From` (което описахме
    // горе), за да създадем `DoubleError`.
    let parsed = first.parse::<i32>()?;

    Ok(2 * parsed)
}

fn print(result: Result<i32>) {
    match result {
        Ok(n)  => println!("Първият удвоен член е {}.", n),
        Err(e) => {
            println!("Грешка: {}", e);
            if let Some(source) = e.source() {
                println!("  Причинена от : {}", source);
            }
        },
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

Този подход изисква повечко писане за обработка на грешките и не винаги е
нужен. Има библиотеки, които могат да спестят осъществяването на обгръщането.
### Вижте също:

[`From::from`][from] и [Броители][enums]

[from]: https://doc.rust-lang.org/std/convert/trait.From.html
[enums]: ../../custom_types/enum.md
