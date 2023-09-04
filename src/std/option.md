# `Option`

Понякога е желателно да прихванем срива на някои части от програмата, вместо да
предизвикваме `panic!`; това може да се постигне с помощта на брояча
`Option`[^Option].

Броячът `Option<T>` има две разновидности:

* `None` – означава неуспех или липса на стойност, и
* `Some(стойност)`, Разнородна структура, която обгръща дадената `стойност` от тип `T`.

```rust,editable,ignore,mdbook-runnable
// Делене на цяло число, което не предизвиква `panic!`
fn checked_division(dividend: i32, divisor: i32) -> Option<i32> {
    if divisor == 0 {
        // Неуспехът се представя като разновидност `None`
        None
    } else {
        // Резултатът е обгърнат в разновидността `Some`
        Some(dividend / divisor)
    }
}

// Тази функция обработва деление, което може да не успее
fn try_division(dividend: i32, divisor: i32) {
    //Стойностите в `Option` може да се проверят чрез израз за намиране на
    // съответствия, като за всеки друг брояч
    match checked_division(dividend, divisor) {
        None => println!("{} / {} failed!", dividend, divisor),
        Some(quotient) => {
            println!("{} / {} = {}", dividend, divisor, quotient)
        },
    }
}

fn main() {
    try_division(4, 2);
    try_division(1, 0);

    // Когато обвързваме `None` с променлива, трябва да отбележим типа
    let none: Option<i32> = None;
    let _equivalent_none = None::<i32>;

    let optional_float = Some(0f32);

    // Трябва да извадим стойността от `Some` чрез разгръщане – `unwrap()`.
    println!("{:?} се разгъва до {:?}", optional_float, optional_float.unwrap());

    // Ако се опитаме да разгърнем разновидност `None`, ще предизвикаме `panic!`.
    println!("{:?} unwraps to {:?}", none, none.unwrap());
}
```

## Б.пр.

[^Option]: `Избор` – `Option`
