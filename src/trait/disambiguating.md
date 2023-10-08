# Уточняване на застъпващи се отличители 

Всеки тип може да осъществява много различни отличители. Но какво да правим,
ако два отличителя изискват осъществяване на метод с едно и също име? Например
много отличители може да имат метод с име `get()`. А тези методи може би връщат
и различен тип данни!

Имаме добри новини. Понеже всяко осъществяване на отличител става в отделен
блок `impl`, винаги е ясно чий метод `get` осъществяваме.

Но пък как ще извикаме тези методи? За да ги различим, се нуждаем от
_Пълнокачествен Правопис_[^fully_qualified].

```rust,editable
trait UsernameWidget {
    // Връща избраното с тази джаджа потребителско име
    fn get(&self) -> String;
}

trait AgeWidget {
    // Връща избраното с тази джаджа потребителско име
    fn get(&self) -> u8;
}

// Форма за попълване с полета UsernameWidget и AgeWidget
struct Form {
    username: String,
    age: u8,
}

impl UsernameWidget for Form {
    fn get(&self) -> String {
        self.username.clone()
    }
}

impl AgeWidget for Form {
    fn get(&self) -> u8 {
        self.age
    }
}

fn main() {
    let form = Form {
        username: "rustacean".to_owned(),
        age: 28,
    };

    // Ако разкоментирате този ред, ще получите грешка, която казва
    // "Намерени са множество `get`", защото наистина е така.
    // println!("{}", form.get());

    let username = <Form as UsernameWidget>::get(&form);
    assert_eq!("rustacean".to_owned(), username);
    let age = <Form as AgeWidget>::get(&form);
    assert_eq!(28, age);
}
```

## Б.пр.

[^fully_qualified]: пълнокачествен правопис – fully qualified syntax. Такова
  описание на нещатата, което не оставя неясноти.

### Вижте също:

[The Ръждьо Programming Language chapter on Fully Qualified syntax][trpl_fqsyntax]

[trpl_fqsyntax]: https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#fully-qualified-syntax-for-disambiguation-calling-methods-with-the-same-name
