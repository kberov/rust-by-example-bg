# Стражи

В `match` може да се добавят *стражи* за допълнителни проверки в разклоненията за сравнение.

```rust,editable
#[allow(dead_code)]
enum Temperature {
    Celsius(i32),
    Fahrenheit(i32),
}

fn main() {
    let temperature = Temperature::Celsius(35);
    // ^ ЗАДАЧА Пробвайте различни стойности за `temperature`

    match temperature {
        Temperature::Celsius(t) if t > 30 => println!("{}°C е над 30 по Целзий", t),
        // Частта с условието `if` ^ е страж
        Temperature::Celsius(t) => println!("{}°C е под 30 по Целзий", t),

        Temperature::Fahrenheit(t) if t > 86 => println!("{}°F е над 86 по Фаренхайт", t),
        Temperature::Fahrenheit(t) => println!("{}°F е под 86 по Фаренхайт", t),
    }
}
```

Забележете, че компилаторът не отчита стражите при проверка за изчерпателност на сравненията.

```rust,editable,ignore,mdbook-runnable
fn main() {
    let number: u8 = 4;

    match number {
        i if i == 0 => println!("Нула"),
        i if i > 0 => println!("По-голямо от нула"),
        // _ => unreachable!("Никога не трябва да се случва."),
        // ЗАДАЧА ^ разкоментирайте, за да се компилира.
    }
}
```

### Вижте също:

[Разнородни поредици](../../primitives/tuples.md),
[Броители](../../custom_types/enum.md)
