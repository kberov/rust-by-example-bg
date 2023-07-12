# Проверка: свързан списък

Често свързаните списъци се осъществяват чрез `enum`:

```rust,editable
use crate::List::*;

enum List {
    // Cons: Списъчна структура, съдържаща един член от списъка и указател към
    // следващия възел
    Cons(u32, Box<List>),
    // Nil: Възел, обозначаващ края на свързания списък
    Nil,
}

// Към брояча могат да се прикачват методи
impl List {
    // Създава празен списък
    fn new() -> List {
        // Типът на `Nil` е `List`
        Nil
    }

    // Приема списък и връща същия списък с нов член в началото му
    fn prepend(self, elem: u32) -> List {
        // Типът на `Cons` също е List
        Cons(elem, Box::new(self))
    }

    // Връща дължината на списъка
    fn len(&self) -> u32 {
        // Трябва да проверим за съответствие със `self`, защото
        // поведението на този метод зависи от варианта на `self`
        // Типът на`self` е `&List`, а типът на `*self` е `List`.
        // За предпочитане е да се търси съответствие с `T` вместо с `&T`.
        // След Ръждьо 2018 тук можем да ползваме `self` както и tail
        // по-долу (без препратка). `&s` и `ref tail` ще бъдат отгатнати. 
        // Вижте https://doc.rust-lang.org/edition-guide/rust-2018/ownership-and-lifetimes/default-match-bindings.html
        match *self {
            // Не можем да овладеем (take ownership of) `tail`, защото `self`
            // е взет на заем; вместо това вземаме препратка към `tail`
            Cons(_, ref tail) => 1 + tail.len(),
            // Основен случай: Празният списък е с нулева дължина
            Nil => 0
        }
    }

    // Връща представяне на списъка като низ (от динамичната памет)
    fn stringify(&self) -> String {
        match *self {
            Cons(head, ref tail) => {
                // `format!` е подобен на `print!`, но връща низ
                // от динамичната памет вместо да печати вконзолата.
                format!("{}, {}", head, tail.stringify())
            },
            Nil => {
                format!("Nil")
            },
        }
    }
}

fn main() {
    // Създаваме празен свързан списък 
    let mut list = List::new();

    // добавяме няколко члена в началото на списъка
    list = list.prepend(1);
    list = list.prepend(2);
    list = list.prepend(3);

    // Показваме окончателното състояние на списъка
    println!("свързаният списък е с дължина {}.", list.len());
    println!("{}", list.stringify());
}
```

### Вижте също:

[`Box`][box] and [methods][methods]

[box]: ../../std/box.md
[methods]: ../../fn/methods.md
