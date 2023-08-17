# Обхождане на `Result`ати

Действието `Iter::map` може да не успее. Например:

```rust,editable
fn main() {
    let strings = vec!["tofu", "93", "18"];
    let numbers: Vec<_> = strings
        .into_iter()
        .map(|s| s.parse::<i32>())
        .collect();
    println!("Results: {:?}", numbers);
}
```

Да видим как може да се подходи.

## Да пропускаме провалените опити с помощта на `filter_map()`

`filter_map` извиква функция и филтрира резултатите, които са `None`.

```rust,editable
fn main() {
    let strings = vec!["tofu", "93", "18"];
    let numbers: Vec<_> = strings
        .into_iter()
        .filter_map(|s| s.parse::<i32>().ok())
        .collect();
    println!("Results: {:?}", numbers);
}
```

## Да съберем провалите с помощта на `map_err()` и `filter_map()`

`map_err` извиква функция , като ѝ подава грешката. Ако добавим това към
предното решение с `filter_map`, можем да съберем грешките, докато обхождаме.

```rust,editable
fn main() {
    let strings = vec!["42", "tofu", "93", "999", "18"];
    let mut errors = vec![];
    let numbers: Vec<_> = strings
        .into_iter()
        .map(|s| s.parse::<u8>())
        .filter_map(|r| r.map_err(|e| errors.push(e)).ok())
        .collect();
    println!("Numbers: {:?}", numbers);
    println!("Errors: {:?}", errors);
}
```

## Да предизвикаме неуспех на цялото действие чрез  `collect()`

`Result` въплъщава `FromIterator`, така че един вектор, съдържащ резултати
(`Vec<Result<T, E>>`) може да бъде превърнат в резултат, съдържащ вектор
(`Result<Vec<T>, E>`). Щом се натъкнем на `Result::Err`, обхождането се
прекратява.

```rust,editable
fn main() {
    let strings = vec!["tofu", "93", "18"];
    let numbers: Result<Vec<_>, _> = strings
        .into_iter()
        .map(|s| s.parse::<i32>())
        .collect();
    println!("Results: {:?}", numbers);
}
```

Същата техника може да бъде приложена с `Option`.

## Събираме всички действителни стойности и грешки с помощта на `partition()`

```rust,editable
fn main() {
    let strings = vec!["tofu", "93", "18"];
    let (numbers, errors): (Vec<_>, Vec<_>) = strings
        .into_iter()
        .map(|s| s.parse::<i32>())
        .partition(Result::is_ok);
    println!("Numbers: {:?}", numbers);
    println!("Errors: {:?}", errors);
}
```

Ако погледнем резултатите, ще забележим, че всичко е все още обгърнато с
`Result`. За да разгърнем стойностите, трябва да напишем още малко код.

```rust,editable
fn main() {
    let strings = vec!["tofu", "93", "18"];
    let (numbers, errors): (Vec<_>, Vec<_>) = strings
        .into_iter()
        .map(|s| s.parse::<i32>())
        .partition(Result::is_ok);
    let numbers: Vec<_> = numbers.into_iter().map(Result::unwrap).collect();
    let errors: Vec<_> = errors.into_iter().map(Result::unwrap_err).collect();
    println!("Numbers: {:?}", numbers);
    println!("Errors: {:?}", errors);
}
```
