# Използване на библиотека

За да свържете кош с тази нова библиотека, може да ползвате флага за `rustc` `--extern`. Всичко от библиотеката ще бъде внесено с име на модул, съответстващо с името на библиотеката. Този модул се държи като всеки друг модул.

```rust,ignore
// extern crate rary; 
// Може да се изисква за издания на Ръждьо 2015 или по-ранни

fn main() {
    rary::public_function();

    // Грешка! `private_function` е частна
    //rary::private_function();

    rary::indirect_access();
}
```

```txt
# library.rlib е пътят до компилираната библиотека. Тук предполагаме,
# че се намира в същата директория:
$ rustc executable.rs --extern rary=library.rlib && ./executable 
called rary's `public_function()`
called rary's `indirect_access()`, that
> called rary's `private_function()`
```
