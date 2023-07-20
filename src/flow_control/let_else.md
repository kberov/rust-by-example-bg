# let-else


> 🛈 за постоянно от: rust 1.65
>
> 🛈 Можете да изберете определено издание като компилирате така
> `rustc --edition=2021 main.rs`


С `let`-`else`, *дадено опровержимо сравнение*[^refutable] може да успее и по
този начин да обвърже променливи от обкръжаващия блока код като обикновено
изявление `let`, или да се разклони (напр. чрез `break`, `return`, `panic!`),
когато сравнението не успее.

```rust
use std::str::FromStr;

fn get_count_item(s: &str) -> (u64, &str) {
    let mut it = s.split(' ');
    let (Some(count_str), Some(item)) = (it.next(), it.next()) else {
        panic!("Can't segment count item pair: '{s}'");
    };
    let Ok(count) = u64::from_str(count_str) else {
        panic!("Can't parse integer: '{count_str}'");
    };
    (count, item)
}

fn main() {
    assert_eq!(get_count_item("3 chairs"), (3, "chairs"));
}
```
Видимостта на обвързаните променливи е главното, по което `let`-`else` се
различава от изразите `match` или `if let`-`else`. Преди въвеждането на
`let`-`else` можеше да се постигне нещо подобно с повече повторения и външно
`let`:

```rust
# use std::str::FromStr;
# 
# fn get_count_item(s: &str) -> (u64, &str) {
#     let mut it = s.split(' ');
    let (count_str, item) = match (it.next(), it.next()) {
        (Some(count_str), Some(item)) => (count_str, item),
        _ => panic!("Can't segment count item pair: '{s}'"),
    };
    let count = if let Ok(count) = u64::from_str(count_str) {
        count
    } else {
        panic!("Can't parse integer: '{count_str}'");
    };
#     (count, item)
# }
# 
# assert_eq!(get_count_item("3 chairs"), (3, "chairs"));
```
Бел. Прев.

[^refutable]: опровержимо сравнение/съвпадение – a refutable pattern. Това е сравнение,
  което може да не успее, например  '0' ..= '9' няма да съвпадне с 'А'.

### Вижте също:

[option][option], [match][match], [if let][if_let] and the [let-else RFC][let_else_rfc].


[match]: ./match.md
[if_let]: ./if_let.md
[let_else_rfc]: https://rust-lang.github.io/rfcs/3137-let-else.html
[option]: ../std/option.md
