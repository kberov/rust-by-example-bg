# Drop

Отличителят [`Drop`][Drop] има само един метод – `drop`, който се извиква
автоматично, когато даден обект излезе от обхват. Главната полза от `Drop` е
освобождаването на памет, която производният обект владее.

`Box`, `Vec`, `String`, `File` и `Process` са примерни въплъщения на `Drop` за
освобождаване на ресурси. Отличителят `Drop` може да бъде осъществен и на ръка
от всеки потребителски тип данни.

Следващият пример отпечатва съобщение в конзолата и така показва кога е
извикана функцията `drop`.

```rust,editable
struct Droppable {
    name: &'static str,
}

// Това нищожно въплъщение на `drop` отпечатва в конзолата.
impl Drop for Droppable {
    fn drop(&mut self) {
        println!("> Dropping {}", self.name);
    }
}

fn main() {
    let _a = Droppable { name: "a" };

    // block A
    {
        let _b = Droppable { name: "b" };

        // block B
        {
            let _c = Droppable { name: "c" };
            let _d = Droppable { name: "d" };

            println!("Exiting block B");
        }
        println!("Just exited block B");

        println!("Exiting block A");
    }
    println!("Just exited block A");

    // Променливата може да бъде освободена чрез извикване ръчно на `drop`
    drop(_a);
    // ЗАДАЧА ^ Коментирайте този ред

    println!("end of the main function");

    // `_a` няма да бъде освободена отново тук, понеже вече е освободена ръчно
}
```

[Drop]: https://doc.rust-lang.org/std/ops/trait.Drop.html
