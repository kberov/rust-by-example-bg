# Функции от по-висок порядък 

Ръждьо предоставя Функции от по-Висок Порядък (ФВП). Това са функции, които
приемат една или повече функции и произвеждат нова, по-полезна функция. ФВП и
ленивите повторители придават на Ръждьо по-функционалния му оттенък.
```rust,editable
fn is_odd(n: u32) -> bool {
    n % 2 == 1
}

fn main() {
    println!("Намираме сбора от нечетните квадрати, по-малки от 1000, \
      на всички числа"
    );
    let upper = 1000;

    // Императивен (със заповеди) подход
    // Обявяваме променлива за съхраняване на сбора
    let mut acc = 0;
    // Изброяваме: 0, 1, 2, ... до безкрайност
    for n in 0.. {
        // Повдигаме числото на квадрат
        let n_squared = n * n;

        if n_squared >= upper {
            // Прекъсваме изброяването, ако сме достигнали горната граница
            break;
        } else if is_odd(n_squared) {
            // Добавяме стойност, ако числото е нечетно
            acc += n_squared;
        }
    }
    println!("Императивен подход: {}", acc);

    // Функционален подход
    let sum_of_squared_odd_numbers: u32 =
        (0..).map(|n| n * n)              // Всички естествени числа на квадрат
             .take_while(|&n_squared| n_squared < upper) // Под горната граница
             .filter(|&n_squared| is_odd(n_squared))     // Които са нечетни
             .sum();                                     // Събираме ги
    println!("Функционален подход: {}", sum_of_squared_odd_numbers);
}
```

[Option][option] и [Iterator][iter] осъществяват немалка част от ФВП.

[option]: https://doc.rust-lang.org/core/option/enum.Option.html
[iter]: https://doc.rust-lang.org/core/iter/trait.Iterator.html
