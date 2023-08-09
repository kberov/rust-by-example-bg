# Обхват и засенчване

Обвързването на дадена променлива е с определен Обхват. Обхватът (в какво
пространство е видима (достъпна) една променлива) е ограничен от обгръщащия я
*блок*. Блокът е набор от изявления, ограден с фигурни скоби `{}`. 
```rust,editable,ignore,mdbook-runnable
fn main() {
    // Това обвързване е в обхват във функцията `main`.
    let long_lived_binding = 1;

    // Това е блок и има по-малък обхват от функцията `main`.
    {
        // Това обвързване съществува само в този блок.
        let short_lived_binding = 2;

        println!("вътрешно краткосрочно: {}", short_lived_binding);
    }
    // Край на блока

    // Грешка! `short_lived_binding` не съществува в този обхват.
    println!("външно краткосрочно обвързване: {}", short_lived_binding);
    // ПОПРАВИ ^ Да се коментира този ред.

    println!("външна дългосрочна: {}", long_lived_binding);
}
```
[Засенчване на променливи][variable-shadow] също е разрешено.
```rust,editable,ignore,mdbook-runnable
fn main() {
    let shadowed_binding = 1;

    {
        println!("Преди засенчване: {}", shadowed_binding);

        // Тази променлива *засенчва* външната.
        let shadowed_binding = "abc";

        println!("засенчена във вътрешния блок: {}", shadowed_binding);
    }
    println!("във външния блок: {}", shadowed_binding);

    // Това обвързване *засенчва* предишното обвързване.
    let shadowed_binding = 2;
    println!("засенчена във външния блок: {}", shadowed_binding);
 
}
```
[variable-shadow]: https://en.wikipedia.org/wiki/Variable_shadowing

