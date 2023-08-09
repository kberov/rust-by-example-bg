# Търсене чрез повторители

`Iterator::find` е функция , която търси чрез обхождане първата стойност,
удовлетворяваща някакво условие. Ако никоя от стойностите в повторителя не
удовлетворява условието, връща `None`. Ето обявлението ѝ:

```rust,ignore
pub trait Iterator {
    // Типът, върху който се извършва повторението.
    type Item;

    // `find` приема `&mut self`, значи извикващият може да бъде
    // заеман и променян, но не и поглъщан
    fn find<P>(&mut self, predicate: P) -> Option<Self::Item> where
        // `FnMut` означава, че всяка прихваната променлива може да бъде
        // най-много променяна, но не и поглъщана. `&Self::Item` покзва, че
        // аргументите към затварянето се приемат като препратки.
        P: FnMut(&Self::Item) -> bool;
}
```

```rust,editable
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec![4, 5, 6];

    //`iter()` за вектори дава `&i32`. 
    let mut iter = vec1.iter();
    // `into_iter()` за вектори дава `i32`.
    let mut into_iter = vec2.into_iter();

    // `iter()` за вектори дава `&i32`, а ние искаме да достъпим някой от
    // членовете. Значи трябва да разложим `&&i32` до `i32`
    println!("Find 2 in vec1: {:?}", iter     .find(|&&x| x == 2));
    // `into_iter()` за вектори дава `i32`, а ние искаме да достъпим някой от
    // членовете. Значи трябва да разложим `&i32` до `i32`
    println!("Find 2 in vec2: {:?}", into_iter.find(| &x| x == 2));

    let array1 = [1, 2, 3];
    let array2 = [4, 5, 6];

    // `iter()` за поредици дава `&&i32`
    println!("Find 2 in array1: {:?}", array1.iter()     .find(|&&x| x == 2));
    // `into_iter()` за поредици дава `&i32`
    println!("Find 2 in array2: {:?}", array2.into_iter().find(|&x| x == 2));
    // ПОПРАВИ: да се промени кода, така че да работи за издание 2021
}
```

`Iterator::find` ви дава препратка към члена. Но ако искате да получите
_мястото му_, използвайте `Iterator::position`.

```rust,editable
fn main() {
    let vec = vec![1, 9, 3, 3, 13, 2];

    // `iter()` за вектори дава `&i32` и `position()` не приема препратка.
    // Значи трябва да разложим `&i32` до `i32``
    let index_of_first_even_number = vec.iter().position(|&x| x % 2 == 0);
    assert_eq!(index_of_first_even_number, Some(5));
    
    // `into_iter()` за вектори дава `i32` и `position()`не приема препратка. 
    // Не трбва да разлагаме
    let index_of_first_negative_number = vec.into_iter().position(|x| x < 0);
    assert_eq!(index_of_first_negative_number, None);
}
```

### See also:

[`std::iter::Iterator::find`][find]

[`std::iter::Iterator::find_map`][find_map]

[`std::iter::Iterator::position`][position]

[`std::iter::Iterator::rposition`][rposition]

[find]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.find
[find_map]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.find_map
[position]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.position
[rposition]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.rposition
