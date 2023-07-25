# Iterator::any

`Iterator::any` е функция, на която като подадем повторител, ще върне `true`, ако
някой от членовете му удовлетворява твърдението. Иначе – `false`. Ето неговото
обявление:

```rust,ignore
pub trait Iterator {
    // Типът, върху който се извършва повторението.
    type Item;

    // `any` приема `&mut self`, което означава, че извикващият може да бъде
    // заеман и променян, но не и употребяван (изяждан - б.пр.)
    fn any<F>(&mut self, f: F) -> bool where
        // `FnMut` означава, че всяка прихваната променлива може да бъде
        // най-много променяна, но не и употребявана. `Self::Item` означава, че
        // аргументите към затварянето се приемат по стойност.
        F: FnMut(Self::Item) -> bool;
}
```

```rust,editable
fn main() {
    let vec1 = vec![1, 2, 3];
    let vec2 = vec![4, 5, 6];

    // `iter()` for vecs yields `&i32`. Destructure to `i32`.
    println!("2 in vec1: {}", vec1.iter()     .any(|&x| x == 2));
    // `into_iter()` for vecs yields `i32`. No destructuring required.
    println!("2 in vec2: {}", vec2.into_iter().any(|x| x == 2));

    // `iter()` only borrows `vec1` and its elements, so they can be used again
    println!("vec1 len: {}", vec1.len());
    println!("First element of vec1 is: {}", vec1[0]);
    // `into_iter()` does move `vec2` and its elements, so they cannot be used again
    // println!("First element of vec2 is: {}", vec2[0]);
    // println!("vec2 len: {}", vec2.len());
    // TODO: uncomment two lines above and see compiler errors.

    let array1 = [1, 2, 3];
    let array2 = [4, 5, 6];

    // `iter()` for arrays yields `&i32`.
    println!("2 in array1: {}", array1.iter()     .any(|&x| x == 2));
    // `into_iter()` for arrays yields `i32`.
    println!("2 in array2: {}", array2.into_iter().any(|x| x == 2));
}
```

### See also:

[`std::iter::Iterator::any`][any]

[any]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.any
