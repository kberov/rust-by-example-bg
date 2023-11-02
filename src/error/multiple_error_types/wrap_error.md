# Обгръщане на грешки

Вместо да слагаме грешките в кутии, можем да ги обгръщаме в наш собствен тип за грешка.

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
                write!(f, "please use a vector with at least one element"),
            // Обгърнатата грешка съдържа допълнителни сведения, а те са
            // достъпни чрез метода source().
            DoubleError::Parse(..) =>
                write!(f, "the provided string could not be parsed as int"),
        }
    }
}

impl error::Error for DoubleError {
    fn source(&self) -> Option<&(dyn error::Error + 'static)> {
        match *self {
            DoubleError::EmptyVec => None,
            // The cause is the underlying implementation error type. Is implicitly
            // cast to the trait обект `&error::Error`. This works because the
            // underlying type already implements the `Error` trait.
            DoubleError::Parse(ref e) => Some(e),
        }
    }
}

// Implement the conversion from `ParseIntError` to `DoubleError`.
// This will be automatically called by `?` if a `ParseIntError`
// needs to be converted into a `DoubleError`.
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
        Ok(n)  => println!("The first doubled is {}", n),
        Err(e) => {
            println!("Error: {}", e);
            if let Some(source) = e.source() {
                println!("  Caused by: {}", source);
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

[`From::from`][from] и [`Enums`][enums]

[from]: https://doc.rust-lang.org/std/convert/trait.From.html
[enums]: ../../custom_types/enum.md
