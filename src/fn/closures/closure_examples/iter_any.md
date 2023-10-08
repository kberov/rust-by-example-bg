# Iterator::any

`Iterator::any` е функция, на която като подадем повторител[^pass_ite], ще върне `true`, ако
някой от членовете му удовлетворява твърдението. Иначе – `false`. Ето неговото
обявление:

```rust,ignore
pub trait Iterator {
    // Типът, върху който се извършва повторението.
    type Item;

    // `any` приема `&mut self`, значи извикващият може да бъде
    // заеман и променян, но не и поглъщан
    fn any<F>(&mut self, f: F) -> bool where
        // `FnMut` означава, че всяка прихваната променлива може най-много
        // да бъде променяна, но не и поглъщана. `Self::Item` означава, че
        // аргументите към затварянето се приемат по стойност.
        F: FnMut(Self::Item) -> bool;
}
```

```rust,editable
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec![4, 5, 6];

    // `iter()` за вектори дава `&i32`.        Привеждаме към `i32`.
    println!("2 in vec1: {}", vec1.iter()     .any(|&x| x == 2));
    // `into_iter()` за вектори дава `i32`.    Няма нужда от привеждане.
    println!("2 in vec2: {}", vec2.into_iter().any(|x| x == 2));

    // `iter()` само заема `vec1` и членовете му, така че могат да бъдат
    // ползвани пак
    println!("vec1 len: {}", vec1.len());
    println!("First element of vec1 is: {}", vec1[0]);
    // `into_iter()` премества `vec2` и членовете му, та не може да се ползват
    // вече
    // println!("First element of vec2 is: {}", vec2[0]);
    // println!("vec2 len: {}", vec2.len());
    // ЗАДАЧА: разкоментирайте двата реда горе и вижте грешките при компилиране

    let array1 = [1, 2, 3];
    let array2 = [4, 5, 6];

    // `iter()` за поредици дава `&i32`.
    println!("2 in array1: {}", array1.iter()     .any(|&x| x == 2));
    // `into_iter()` за поредици дава `i32`.
    println!("2 in array2: {}", array2.into_iter().any(|x| x == 2));
    // ^ ПОПРАВИ: да се промени кода, така че да работи за издание 2021
}
```
## Бел. прев.

[^pass_ite]: подаване на повторител като аргумент – passing an iterator to a function. Повторителят се подава като първи аргумент, а твърдението, което е
  затваряне, като втори.

[^yield]: произвеждам, давам, нося – yield. https://eurodict.com/dictionary/yield-82257

### Вижте също:

[`std::iter::Iterator::any`][any]

[any]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.any
