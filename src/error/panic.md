# `panic`

Най-простата обработка на грешки  е `panic`. Този макрос извежда съобщение за
грешка, започва да размотава стълпа и обикновено прекратява програмата. Тук
изрично извикваме `panic` при нашето условие за грешка:

```rust,editable,ignore,mdbook-runnable
fn drink(beverage: &str) {
    // Не бива да пиете твърде много сладки напитки.
    if beverage == "lemonade" { panic!("AAAaaaaa!!!!"); }

    println!("Просто ми трябва нещо освежаващо като {}.", beverage);
}

fn main() {
    drink("water");
    drink("lemonade");
    drink("still water");
}
```

Първото извикване на `drink` работи, а второто се паникьосва, така че до третото не
достигаме никога.
