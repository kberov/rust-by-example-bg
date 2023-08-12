# Отличители

Отличителят (буквално отличителна черта – б. пр.) (`trait`) е набор от методи,
описани за непознат тип: `Self`. Методите могат да достъпват други методи в същия
отличител.

Отличителите могат да бъдат осъществявани за всеки тип данни. В този пример описваме
`Animal` като множество от методи. След това отличителят `Animal` бива осъществен за
типа `Sheep`. Така можем да ползваме методи от `Animal` с `Sheep`.

```rust,editable
struct Sheep { naked: bool, name: &'static str }

trait Animal {
    // Обявяваме придадена функция; `Self` се отнася до осъществяващия тип
    fn new(name: &'static str) -> Self;

    // Обяви на методи; ще върнат низ.
    fn name(&self) -> &'static str;
    fn noise(&self) -> &'static str;

    // Отличителите могат да предоставят осъществени методи по подразбиране.
    fn talk(&self) {
        println!("{} says {}", self.name(), self.noise());
    }
}

impl Sheep {
    fn is_naked(&self) -> bool {
        self.naked
    }

    fn shear(&mut self) {
        if self.is_naked() {
            // Методите на осъществителя (структура, друг отличител) могат да
            // извикват методите, осъществени в отличителя.
            println!("{} is already naked...", self.name());
        } else {
            println!("{} gets a haircut!", self.name);

            self.naked = true;
        }
    }
}

// Осъществяваме отличителя `Animal` за `Sheep`.
impl Animal for Sheep {
    // `Self` е типа на осъществителя: `Sheep`.
    fn new(name: &'static str) -> Sheep {
        Sheep { name: name, naked: false }
    }

    fn name(&self) -> &'static str {
        self.name
    }

    fn noise(&self) -> &'static str {
        if self.is_naked() {
            "беееее?"
        } else {
            "бееееее!"
        }
    }
    
    // Методите по подразбиране, осъществени в отличителя могат да бъдат
    // променени.
    fn talk(&self) {
        // Например можем да добавим малко тихо съзерцание.
        println!("{} pauses briefly... {}", self.name, self.noise());
    }
}

fn main() {
    // В този случай е нужно да се укаже типа.
    let mut dolly: Sheep = Animal::new("Dolly");
    // ЗАДАЧА ^ Премахнете необходимостта от указване на типа на променливата.

    dolly.talk();
    dolly.shear();
    dolly.talk();
}
```
