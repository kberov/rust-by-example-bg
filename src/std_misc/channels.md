# Канали

Ръждьо предоставя _неедновременни[^asynchronous] канали (`channels`)_ за
_общуване_[^communication] между нишките. Каналите предоставят еднопосочен
поток данни между две крайни точки: Подател (`Sender`) и Получател
(`Receiver`).

```rust,editable
use std::sync::mpsc::{Sender, Receiver};
use std::sync::mpsc;
use std::thread;

static NTHREADS: i32 = 3;

fn main() {
    // Каналите имат две крайни точки: `Sender<T>` и `Receiver<T>`,
    // където `T` е типа съобщение, което се пренася
    // (означаването на типа е излишно).
    let (tx, rx): (Sender<i32>, Receiver<i32>) = mpsc::channel();
    let mut children = Vec::new();

    for id in 0..NTHREADS {
        // Точката подател може да се копира.
        let thread_tx = tx.clone();

        // Всяка нишка ще подаде своето 'id' през канала.
        let child = thread::spawn(move || {
            // Нишката овладява `thread_tx`
            // Всяка нишка слага на опашка съобщение в канала.
            thread_tx.send(id).unwrap();

            // Подаването е неблокиращо действие. Нишката ще продължи незабавно
            // след подаването на своето съобщение.
            println!("Нишка {} приключи.", id);
        });

        children.push(child);
    }

    // Тук се събират всички съобщения.
    let mut ids = Vec::with_capacity(NTHREADS as usize);
    for _ in 0..NTHREADS {
        // Методът `recv` взима съобщение от канала.
        // `recv` блокира текущата нишка, ако няма пристигнали съобщения.
        ids.push(rx.recv());
    }
    
    // Чакаме нишките да довършат каквото има.
    for child in children {
        child.join().expect("Опаа! Дъщерната нишка се паникьоса.");
    }

    // Показваме съобщенията в реда на изпращане.
    println!("{:?}", ids);
}
```

## Б. пр.

[^asynchronous]: неедновременни – asynchronous; https://www.etymonline.com/word/synchronous https://www.etymonline.com/word/asynchronous

[^communication]: общуване – communication
