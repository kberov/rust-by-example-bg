# Предели

Както обобщените типове могат да имат предели, животите (сами по себе си
обобщения) също ползват предели. Знакът `:` тук има малко по различно значение,
но пък знакът `+` е със същото значение като при обобщенията. Тук следното се
чете така:

1. `T: 'a`: *Всички* препратки в `T` трябва да имат продължителност на живота
   по-голяма от `'a`.
2. `T: Trait + 'a`: Типът `T` трябва да осъществява отличителя `Trait` и
   *всички* препратки в `T` трябва да живеят по-продължително от `'a`.

Примерът показва използването на горния правопис след ключовата дума `where`:

```rust,editable
use std::fmt::Debug; // Trait to bound with.

#[derive(Debug)]
struct Ref<'a, T: 'a>(&'a T);
// `Ref` съдържа препратка към обобщен тип `T`, който има някакъв живот `'a`.
//`T` е о-пределен (ограничен) така, че всякакви *препратки* в `T` трябва да
// надживеят `'a`. Освен това животът на `Ref` не може да надвишава `'a`.

// Обобщена функция, която печати с помощта на отличителя `Debug`.
fn print<T>(t: T) where
    T: Debug {
    println!("`print`: t is {:?}", t);
}

// Тук функцията приема препратка към `T`, където `T` осъществява `Debug` и
// всички препратки в `T` живеят повече от `'a`. Освен това `'a` трябва да
// надживее функцията.
fn print_ref<'a, T>(t: &'a T) where
    T: Debug + 'a {
    println!("`print_ref`: t is {:?}", t);
}

fn main() {
    let x = 7;
    let ref_x = Ref(&x);

    print_ref(&ref_x);
    print(ref_x);
}
```

### Вижте също:

[„Обобщения”][generics], [„Предели” в „Обобщения”][bounds] и
[„Множество предели” в „Обобщения”][multibounds]

[generics]: ../../generics.md
[bounds]: ../../generics/bounds.md
[multibounds]: ../../generics/multi_bounds.md
