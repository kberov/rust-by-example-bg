# Грешки в Кутии

Един начин за писане на прост код, запазвайки първоначалните грешки, е да
ползваме [`Кутии`][box]. За съжаление в този случай истинската грешка става
известна едва по време на изпълнение и не е [определена
статично][dynamic_dispatch].

С помощта на стандартната библиотека можем да слагаме нашите грешки в кутии.
`Box` може да превръща от всеки тип, осъществяващ отличителя `Error` в обект от
тип `Box<Error>` чрез [`From`][from].

```rust,editable
use std::error;
use std::fmt;

// Променяме прякора на `Box<error::Error>`.
type Result<T> = std::result::Result<T, Box<dyn error::Error>>;

#[derive(Debug, Clone)]
struct EmptyVec;

impl fmt::Display for EmptyVec {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

impl error::Error for EmptyVec {}

fn double_first(vec: Vec<&str>) -> Result<i32> {
    vec.first()
        .ok_or_else(|| EmptyVec.into()) // Превръща в Box
        .and_then(|s| {
            s.parse::<i32>()
                .map_err(|e| e.into()) // Превръща в Box
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

### Вижте също:

[Dynamic dispatch][dynamic_dispatch] and [`Error` trait][error]

[box]: https://doc.rust-lang.org/std/boxed/struct.Box.html
[dynamic_dispatch]: https://doc.rust-lang.org/book/ch17-02-trait-objects.html#trait-objects-perform-dynamic-dispatch
[error]: https://doc.rust-lang.org/std/error/trait.Error.html
[from]: https://doc.rust-lang.org/std/convert/trait.From.html
