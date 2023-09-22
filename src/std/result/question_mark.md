# `?`

Навръзването на резултати с `match` може да се окаже трудно четимо. За щастие
операторът `?` може да подобри четимостта. `?` се ползва в края на изрази,
които връщат стойност обгърната в `Result`, и е същото като в израза `match`,
като разклонението за обработка на резултата `Err(err)` се изпълнява като ранно
връщане – `return Err(From::from(err))`, а разклонението `Ok(ok)` връща израз
`ok`.

```rust,editable,ignore,mdbook-runnable
mod checked {
    #[derive(Debug)]
    enum MathError {
        DivisionByZero,
        NonPositiveLogarithm,
        NegativeSquareRoot,
    }

    type MathResult = Result<f64, MathError>;

    fn div(x: f64, y: f64) -> MathResult {
        if y == 0.0 {
            Err(MathError::DivisionByZero)
        } else {
            Ok(x / y)
        }
    }

    fn sqrt(x: f64) -> MathResult {
        if x < 0.0 {
            Err(MathError::NegativeSquareRoot)
        } else {
            Ok(x.sqrt())
        }
    }

    fn ln(x: f64) -> MathResult {
        if x <= 0.0 {
            Err(MathError::NonPositiveLogarithm)
        } else {
            Ok(x.ln())
        }
    }

    // Междинна функция 
    fn op_(x: f64, y: f64) -> MathResult {
        // Ако `div` не мине, се връща `DivisionByZero`.
        let ratio = div(x, y)?;

        // Ако `ln` не мине, връщаме `NonPositiveLogarithm`
        let ln = ln(ratio)?;

        sqrt(ln)
    }

    pub fn op(x: f64, y: f64) {
        match op_(x, y) {
            Err(why) => panic!("{}", match why {
                MathError::NonPositiveLogarithm
                    => "Логаритъм от отрицателно число!",
                MathError::DivisionByZero
                    => "Деление на нула!",
                MathError::NegativeSquareRoot
                    => "Корен квадратен от отрицателно число!",
            }),
            Ok(value) => println!("{}", value),
        }
    }
}

fn main() {
    checked::op(1.0, 10.0);
}
```

Не забравяйте да прогледнете [документацията][docs], тъй като там има много
методи за обвързване и създаване на `Result`.

[docs]: https://doc.rust-lang.org/std/result/index.html
