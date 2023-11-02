# Други ползвания на `?`

Забележете, че в предния пример след разбора гледаме незабавно да сложим
грешката от библиотеката в кутия:

```rust,ignore
.and_then(|s| s.parse::<i32>())
    .map_err(|e| e.into())
```

Тъй като това е просто действие, може да се окаже удобно да можем да го
пропускаме. За съжаление, понеже `and_then` не е достатъчно гъвкава, това е
невъзможно. Вместо това можем да ползваме `?`.

Оперторът `?` вече бе описан като „`unwrap` или `return Err(err)`”.
Това не е съвсем вярно. `?` всъщност означава „`unwrap` или `return
Err(From::from(err))`”. Понеже `From::from` е средство за превръщане между
различни типове, това означава, че ако извикаме оператора `?`, където грешката
може да бъде превърната в типа за връщане, той ще я превърне автоматично.

Тук пренаписваме предния пример да ползва `?`. От това следва, че `map_err` вече
не ни е нужна, щом `From::from` е осъществена за нашия тип за грешка:

```rust,editable
use std::error;
use std::fmt;

// Променяме прякора за типа грешка на `Box<dyn error::Error>`.
type Result<T> = std::result::Result<T, Box<dyn error::Error>>;

#[derive(Debug)]
struct EmptyVec;

impl fmt::Display for EmptyVec {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "invalid first item to double")
    }
}

impl error::Error for EmptyVec {}

// Същото построение като преди, но вместо да направим всички `Result` и
// `Option` верижни, ползваме оператора `?`, за да вземем и върнем вътрешната
// стойност незабавано.
fn double_first(vec: Vec<&str>) -> Result<i32> {
    let first = vec.first().ok_or(EmptyVec)?;
    let parsed = first.parse::<i32>()?;
    Ok(2 * parsed)
}

fn print(result: Result<i32>) {
    match result {
        Ok(n)  => println!("The first doubled is {}", n),
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

Сега е доста по-чистичко. В сравнение с първоначалната `panic`-а, това много
наподобява на заместване на извикванията на `unwrap` със `?`, само дето типът за
връщане е `Result`. И сега само трябва да бъдат разложени при получване от извикващия.

### Вижте също:

[`From::from`][from] и [`?`][q_mark]

[from]: https://doc.rust-lang.org/std/convert/trait.From.html
[q_mark]: https://doc.rust-lang.org/reference/expressions/operator-expr.html#the-question-mark-operator
