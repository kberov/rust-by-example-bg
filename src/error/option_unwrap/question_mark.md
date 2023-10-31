# Разопаковане на избори със `?`

Може да разопаковате `Option` с изявления `match`, но често е по-лесно да се
ползва операторът `?`. Ако `x` е `Option`, то тогава, като изпълните `x?`, то
ще върне вътрешната стойност, ако `x` е `Some`. Иначе ще прекъсне функцията, в
която се намира, и ще върне `None`.

```rust,editable
fn next_birthday(current_age: Option<u8>) -> Option<String> {
	// Ако `current_age` е `None`, това връща `None`.
	// Ако `current_age` е `Some`, вътрешната стойност от тип `u8` + 1
    // бива присвоена на `next_age`
    let next_age: u8 = current_age? + 1;
    Some(format!("Следващата година ще съм на {} години.", next_age))
}
```

Може да навържете много `?` един след друг, за да направите кода си по-четим.

```rust,editable
#![allow(dead_code)]
#[derive(Debug)]
struct Person {
    job: Option<Job>,
}

#[derive(Clone, Copy, Debug)]
struct Job {
    phone_number: Option<PhoneNumber>,
}

#[derive(Clone, Copy, Debug)]
struct PhoneNumber {
    area_code: Option<u8>,
    number: u32,
}

impl Person {
    // Взима областния код от служебния телефонен номер на лицето, ако го има.
    fn work_phone_area_code(&self) -> Option<u8> {
        // Без оператора `?` тук щеше да има много вгнездени едно в друго
        // изявления `match`.
        // Кодът щеше да е много повече. Опитайте да го напишете сами и
        // вижте кое е по-лесно.
        self.job?.phone_number?.area_code
    }
}

fn main() {
    let p = Person {
        job: Some(Job {
            phone_number: Some(PhoneNumber {
                area_code: Some(61),
                number: 439222222,
            }),
        }),
    };

    println!("{p:#?}");
    assert_eq!(p.work_phone_area_code(), Some(61));
}

```
