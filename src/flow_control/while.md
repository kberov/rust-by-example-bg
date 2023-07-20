# while

Ключовата дума `while` (докато) може да се използва за изпълнение на цикъл,
докато някакво условие е истина.

Да напишем непопулярната програма [FizzBuzz][fizzbuzz] като изпозлваме цикъл`while`.

```rust,editable
fn main() {
    // Промелива за броене
    let mut n = 1;

    // Въртим, докато `n` е по-малко от 101
    while n < 101 {
        if n % 15 == 0 {
            println!("fizzbuzz");
        } else if n % 3 == 0 {
            println!("fizz");
        } else if n % 5 == 0 {
            println!("buzz");
        } else {
            println!("{}", n);
        }

        // Увеличаваме стойността на брояча
        n += 1;
    }
}
```

[fizzbuzz]: https://en.wikipedia.org/wiki/Fizz_buzz
