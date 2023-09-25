# Входни функции

Понеже затварянията могат да бъдат използвани като аргументи, може би се чудите
дали същото важи и за функциите. И наистина – да! Ако обявите функция, приемаща
затваряне като параметър, тогава всяка функция, удовлетворяваща *пределите*[^bounds] на
отличителя за това затваряне, може да бъде подадена като параметър.

```rust,editable
// Описваме функция, която приема обобщен аргумент `F` о-пределен от `Fn`,
// и го извиква като функция
fn call_me<F: Fn()>(f: F) {
    f();
}

// Описваме функция-обвивка, удовлетворяваща пределите на `Fn`
fn function() {
    println!("I'm a function!");
}

fn main() {
    // Описваме затваряне, удовлетворяващо пределите на `Fn`
    let closure = || println!("I'm a closure!");

    call_me(closure);
    call_me(function);
}
```

В допълнение отличителите (`traits`) `Fn`, `FnMut`, и `FnOnce` нареждат как
затварянето прихваща променливите от околния видим обхват.

[^bounds]: предели – bounds

### See also:

[`Fn`][fn], [`FnMut`][fn_mut], и [`FnOnce`][fn_once]

[fn]: https://doc.rust-lang.org/std/ops/trait.Fn.html
[fn_mut]: https://doc.rust-lang.org/std/ops/trait.FnMut.html
[fn_once]: https://doc.rust-lang.org/std/ops/trait.FnOnce.html
