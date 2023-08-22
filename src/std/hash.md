# HashMap

Докато векторите съхраняват стойностите си в поредица, в която всяка стойност
се достъпва чрез показалец – поредното ѝ число, _безредиците_[^hash] (`HashMap`)
съхраняват стойностите си с показалци-ключове. Безредните ключове могат да
бъдат булеви стойности, цели числа, низове или всеки друг тип, осъществяващ
отличителите `Eq` и `Hash`. Повече за това ще научим в следващия радел. 

Както и векторите, безредиците могат да растат, но могат и сами да се свиват,
когато заемат излишно пространство. Можем да създадем безредица
с някаква начална вместимост с помощта на `HashMap::with_capacity(uint)` или
`HashMap::new()` с някаква вместимост по подразбиране (препоръчва се).

```rust,editable
use std::collections::HashMap;

fn call(number: &str) -> &str {
    match number {
        "798-1364" => "We're sorry, the call cannot be completed as dialed. 
            Please hang up and try again.",
        "645-7689" => "Hello, this is Mr. Awesome's Pizza. My name is Fred.
            What can I get for you today?",
        _ => "Hi! Who is this again?"
    }
}

fn main() { 
    let mut contacts = HashMap::new();

    contacts.insert("Daniel", "798-1364");
    contacts.insert("Ashley", "645-7689");
    contacts.insert("Katie", "435-8291");
    contacts.insert("Robert", "956-1745");

    // Takes a reference and returns Option<&V>
    match contacts.get(&"Daniel") {
        Some(&number) => println!("Calling Daniel: {}", call(number)),
        _ => println!("Don't have Daniel's number."),
    }

    // `HashMap::insert()` returns `None`
    // if the inserted value is new, `Some(value)` otherwise
    contacts.insert("Daniel", "164-6743");

    match contacts.get(&"Ashley") {
        Some(&number) => println!("Calling Ashley: {}", call(number)),
        _ => println!("Don't have Ashley's number."),
    }

    contacts.remove(&"Ashley"); 

    // `HashMap::iter()` returns an iterator that yields 
    // (&'a key, &'a value) pairs in arbitrary order.
    for (contact, &number) in contacts.iter() {
        println!("Calling {}: {}", contact, call(number)); 
    }
}
```

За повече сведения, свързани с хеширането и хеш-съответствията (понякога
наричани хеш-таблици) погледнете [Хеш таблица в Уикипедия][wiki-hash]

## Б.пр.

[^hash]: безрѐдица, каша, безредни съответствия, таблицa със съответствия ключ-тойност – hash-map, hash-table 

[wiki-hash]:https://bg.wikipedia.org/wiki/Хеш_таблица 
