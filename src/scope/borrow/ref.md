# Образецът `ref`

Когато намираме съвпадения по образец, или разлагаме съставни променливи като
обвързваме имена на променливи с данни чрез `let`, можем да ползваме ключовата
дума `ref`, за да вземем препратка към полетата на структурата или разнородния
списък. Следващият пример ни показва няколко случая, в които това може да бъде
полезно. 

```rust,editable
#[derive(Clone, Copy)]
struct Point { x: i32, y: i32 }

fn main() {
    let c = 'Q';

    // Заемане чрез използване на `ref` от лявата страна на `=` е
    // същото като заемане чрез използване на `&` от дясната страна.
    let ref ref_c1 = c;
    let ref_c2 = &c;

    println!("ref_c1 equals ref_c2: {}", *ref_c1 == *ref_c2);

    let point = Point { x: 0, y: 0 };

    // `ref` действа също при разлагане на структура.
    let _copy_of_x = {
        // `ref_to_x` е препратка към полето `x` в `point`.
        let Point { x: ref ref_to_x, y: _ } = point;

        // Връщаме копие на полето `x` от `point`.
        *ref_to_x
    };

    // Менимо копие от `point`
    let mut mutable_point = point;

    {
        // `ref` може да се ползва с `mut` за взимане на меними препратки.
        let Point { x: _, y: ref mut mut_ref_to_y } = mutable_point;

        // Променяме стойността на полето `y` от `mutable_point` чрез менима препратка.
        *mut_ref_to_y = 1;
    }

    println!("point is ({}, {})", point.x, point.y);
    println!("mutable_point is ({}, {})", mutable_point.x, mutable_point.y);

    // Меним разнореден списък, който включва указател
    let mut mutable_tuple = (Box::new(5u32), 3u32);
    
    {
        // Разлагаме `mutable_tuple`. за да променим стойността на `last`.
        let (_, ref mut last) = mutable_tuple;
        *last = 2u32;
    }
    
    println!("tuple is {:?}", mutable_tuple);
}
```
