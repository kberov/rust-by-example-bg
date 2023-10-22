# Повторители

Отличителят [`Iterator`][iter] се ползва за осъществяване на повторители върху
сбирки от данни, каквито са поредиците.

Отличителят изисква само един метод за следващия (`next`) член. Той може да се
опише на ръка в блок за осъществяване (`impl`) или да се ползва автоматично
(както е в поредиците и редиците).

За удобство в общия случай конструкцията `for` превръща някои сбирки в
повторители с помощта на метода [`.into_iter()`][intoiter].

```rust,editable
struct Fibonacci {
    curr: u32,
    next: u32,
}

// Осъществяваме `Iterator` за `Fibonacci`.
// Отличителят `Iterator` изисква осъществяване само на един метод за следващия
// (`next`) член.
impl Iterator for Fibonacci {
    // Можем да ползваме този тип чрез Self::Item
    type Item = u32;

    // Тук описваме последователност с `.curr` и `.next`.
    // Типът за връщане е `Option<T>`:
    //     * Когато `Iterator` свърши, ще бъде върнато `None`.
    //     * Иначе – следващата стойност бива обгърната в `Some` и върната.
    // Ползваме Self::Item като тип за връщане, така че можем да сменим типа и
    // да не променяме обявленията на функциите.
    fn next(&mut self) -> Option<Self::Item> {
        let current = self.curr;

        self.curr = self.next;
        self.next = current + self.next;

        // Тъй като редицата на Фибоначи няма край, то `Iterator` никога няма да върне
        // `None`, а винаги ще връща `Some`.
        Some(current)
    }
}

// Връща производител на последователност на Фибоначи.
fn fibonacci() -> Fibonacci {
    Fibonacci { curr: 0, next: 1 }
}

fn main() {
    // `0..3` е `Iterator`, който създава 0, 1, and 2.
    let mut sequence = 0..3;

    println!("Четири последователни извиквания на `next` върху редицата 0..3");
    println!("> {:?}", sequence.next());
    println!("> {:?}", sequence.next());
    println!("> {:?}", sequence.next());
    println!("> {:?}", sequence.next());

    // `for` работи върху обект, създаден от `Iterator`, докато той не върне `None`.
    // Всяка стойност `Some` е разгърната и обвързана с променлива (тук – `i`).
    println!("Iterate through 0..3 using `for`");
    for i in 0..3 {
        println!("> {}", i);
    }

    // Методът `take(n)` смалява обекта от тип `Iterator` до първите му `n` члена.
    println!("Първите четири члена от последователността на Фибоначи са: ");
    for i in fibonacci().take(4) {
        println!("> {}", i);
    }

    // Методът `skip(n)` скъсява обекта от тип `Iterator`, като освобождава първите му `n` члена.
    println!("Следващите четири члена от последователността на Фибоначи са: ");
    for i in fibonacci().skip(4).take(4) {
        println!("> {}", i);
    }

    let array = [1u32, 3, 3, 7];

    // Методът `iter` произвежда обект от тип `Iterator` от поредица или отрязък.
    println!("Iterate the following array {:?}", &array);
    for i in array.iter() {
        println!("> {}", i);
    }
}
```


[intoiter]: https://doc.rust-lang.org/std/iter/trait.IntoIterator.html
[iter]: https://doc.rust-lang.org/core/iter/trait.Iterator.html
