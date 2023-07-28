# Като изходни параметри 

Възможно е да се приемат затваряния като входни параметри, значи би трябвало да
е възможно и да връщаме затваряния. Но, както занем, типовете на затварянията
нямат имена и са незвестни, от което следва, че трябва да опишем типа за връщане
като `impl ИмеНаОтличител`, за да върнем затваряне.

Възможните отличители за връщане на затваряне:

* `Fn`
* `FnMut`
* `FnOnce`

Освен това, трябва да се ползва ключовата дума `move`. Това подсказва, че
всички прихващания са по стойност. Това се налага, понеже прихващанията като
препратки ще бъдат изхвърлени (паметта ще бъде освободена (б. пр.)), когато
функцията върне и така биха останали недействителни препратки във върнатото
затваряне.

```rust,editable
fn create_fn() -> impl Fn() {
    let text = "Fn".to_owned();

    move || println!("This is a: {}", text)
}

fn create_fnmut() -> impl FnMut() {
    let text = "FnMut".to_owned();

    move || println!("This is a: {}", text)
}

fn create_fnonce() -> impl FnOnce() {
    let text = "FnOnce".to_owned();

    move || println!("This is a: {}", text)
}

fn main() {
    let fn_plain = create_fn();
    let mut fn_mut = create_fnmut();
    let fn_once = create_fnonce();

    fn_plain();
    fn_mut();
    fn_once();
}
```

### See also:

[`Fn`][fn], [`FnMut`][fnmut], [Generics][generics] and [impl ИмеНаОтличител][impltrait].

[fn]: https://doc.rust-lang.org/std/ops/trait.Fn.html
[fnmut]: https://doc.rust-lang.org/std/ops/trait.FnMut.html
[generics]: ../../generics.md
[impltrait]: ../../trait/impl_trait.md
