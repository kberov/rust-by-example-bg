# `Result`

Видяхме, че броячът `Option` може да се ползва като стойност за връщане от
функции, който може да не успеят, като можем да върнем `None` при неуспех.
Понякога, обаче, е важно да кажем  _защо_ действието не е успяло. За тази цел
разполагаме с брояча `Result` – `Изход|Резултат`.

`Result<T, E>` има две разновидности:

* `Ok(стойност)`, което означава, че действието е успяло и обгръща
  `стойност`та, върната от действието. (`стойност` е от тип `T`);
* `Err(защо)`, което означава, че действието е неуспешно и обгръща `защо`, и
  което (надяваме се) обяснява причината за неуспеха. (`защо` е от тип `E`).

```rust,editable,ignore,mdbook-runnable
mod checked {
    // Математически „грешки”, който искаме да прихванем
    #[derive(Debug)]
    pub enum MathError {
        DivisionByZero,
        NonPositiveLogarithm,
        NegativeSquareRoot,
    }

    pub type MathResult = Result<f64, MathError>;

    pub fn div(x: f64, y: f64) -> MathResult {
        if y == 0.0 {
            // Това действие може да не успее. Нека върнем причината обгърната
            // в `Err`
            Err(MathError::DivisionByZero)
        } else {
            // Това действие е правилно – връщаме изхода от него загърнат в
            // `Ok`.
            Ok(x / y)
        }
    }

    pub fn sqrt(x: f64) -> MathResult {
        if x < 0.0 {
            Err(MathError::NegativeSquareRoot)
        } else {
            Ok(x.sqrt())
        }
    }

    pub fn ln(x: f64) -> MathResult {
        if x <= 0.0 {
            Err(MathError::NonPositiveLogarithm)
        } else {
            Ok(x.ln())
        }
    }
}

// `op(x, y)` === `sqrt(ln(x / y))`
fn op(x: f64, y: f64) -> f64 {
    // Това е пирмида за намиране на съвпадения на три нива!
    match checked::div(x, y) {
        Err(why) => panic!("{:?}", why),
        Ok(ratio) => match checked::ln(ratio) {
            Err(why) => panic!("{:?}", why),
            Ok(ln) => match checked::sqrt(ln) {
                Err(why) => panic!("{:?}", why),
                Ok(sqrt) => sqrt,
            },
        },
    }
}

fn main() {
    // Will this fail?
    println!("{}", op(1.0, 10.0));
}
```
