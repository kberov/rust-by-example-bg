# Отличители

Отличителят (буквално отличителна черта – б. пр.) (`trait`) е набор от методи,
описани за непознат тип: `Self`. Те могат да достъпват други методи в същия
отличител.

Отличителите могат да бъдат осъществени за всеки тип данни. В този пример описваме
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

    // Traits can provide default method definitions.
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
            // Implementor methods can use the implementor's trait methods.
            println!("{} is already naked...", self.name());
        } else {
            println!("{} gets a haircut!", self.name);

            self.naked = true;
        }
    }
}

// Implement the `Animal` trait for `Sheep`.
impl Animal for Sheep {
    // `Self` is the implementor type: `Sheep`.
    fn new(name: &'static str) -> Sheep {
        Sheep { name: name, naked: false }
    }

    fn name(&self) -> &'static str {
        self.name
    }

    fn noise(&self) -> &'static str {
        if self.is_naked() {
            "baaaaah?"
        } else {
            "baaaaah!"
        }
    }
    
    // Default trait methods can be overridden.
    fn talk(&self) {
        // For example, we can add some quiet contemplation.
        println!("{} pauses briefly... {}", self.name, self.noise());
    }
}

fn main() {
    // Type annotation is necessary in this case.
    let mut dolly: Sheep = Animal::new("Dolly");
    // TODO ^ Try removing the type annotations.

    dolly.talk();
    dolly.shear();
    dolly.talk();
}
```
