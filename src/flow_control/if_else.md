# if/else

Разклоняването чрез `if`-`else` е подобно на другите езици. За разлика от много
от тях, условието не е нужно да бъде оградено със скоби и всяко условно
разклонение е задължително следвано от блок. Условията `if`-`else` са *изрази*[^expressions] и
всички разклонения трябва да връщат данни с един и същи тип.

```rust,editable
fn main() {
    let n = 5;

    if n < 0 {
        print!("{} е отрицателно", n);
    } else if n > 0 {
        print!("{} е положително", n);
    } else {
        print!("{} е нула", n);
    }

    let big_n =
        if n < 10 && n > -10 {
            println!(", и е малко число, да го увеличим  десет пъти…");

            // Този израз връща данни от тип `i32`.
            10 * n
        } else {
            println!(", и е голямо число, да го намалим наполовина…");

            // Този израз трябва също да върне `i32`.
            n / 2
            // ЗАДАЧА ^ Опитайте да подтиснете този израз с точка и запетая.
        };
    //   ^ Не забравяйте да поставите ; тук!
    // Всички обвързвания на променливи го изискват.

    println!("{} -> {}", n, big_n);
}
```

### Вижте също
[Изрази][expressions]

## Б. пр.
[^expressions]: ако-то – `if`-`else` са изрази. Това означава, че връщат стойност.


[expressions]: ../../expression.md 
