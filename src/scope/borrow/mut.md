# Менѝмост

Менѝмите данни могат да бъдат заемани менѝмо като напишем `&mut T`. Това се
нарича _менѝма препратка_[^mut_ref] и дава на заемащата променлива достъп до
стойността за пиасне и четене. Обратно на това, като напишем `&T`, заемаме
данните като _неменѝма препратка_[^immut_ref]. Заемащата променлива може само
да чете данните, но не и да ги променя.

```rust,editable,ignore,mdbook-runnable
#[allow(dead_code)]
#[derive(Clone, Copy)]
struct Book {
    // `&'static str` е препратка към низ в паметта само за четене
    author: &'static str,
    title: &'static str,
    year: u32,
}

// Тази функция взема препратка към променлива от тип Book
fn borrow_book(book: &Book) {
    println!(
        "Аз взех на заем неменѝмо {} - {} edition",
        book.title, book.year
    );
}

// Тази функция взема менима препратка към менима `book` и променя полето
// `year` на 2014
fn new_edition(book: &mut Book) {
    book.year = 2014;
    println!("Аз заех менѝмо {} - издание {}", book.title, book.year);
}

fn main() {
    // Създаваме неменѝма Book, наречена `immutabook`
    let immutabook = Book {
        // буквалните низове са от тип `&'static str`
        author: "Douglas Hofstadter",
        title: "Gödel, Escher, Bach",
        year: 1979,
    };

    // Създаваме менѝмо копие на `immutabook` и го наричаме `mutabook`
    let mut mutabook = immutabook;

    // Заемаме неменимо неменим обект
    borrow_book(&immutabook);

    // Заемаме неменимо меним обект
    borrow_book(&mutabook);

    // Заемаме менимо меним обект
    new_edition(&mut mutabook);

    // Error! Не може да се заеме неменим обект като менѝм
    new_edition(&mut immutabook);
    // ПОПРАВИ ^ Коментирайте този ред
}
```

## Б.пр.

[^mut_ref]: менѝма препратка – mutable reference

[^immut_ref]: неменѝма препратка – immutable reference

### See also:
[`static`][static]

[static]: ../lifetime/static_lifetime.md
