# Box, кама̀ра и куп

По подразбиране Ръждьо слага всички стойности на кама̀ра в паметта. Стойностите може да
се сложат в _кутии (`Box<T>`)_ и така да се сложат в купа̀. Кутията (`Box<T>`) е
умен указател към стойност от тип `T`, за която е заделено място в купа̀. Когато
кутия излезе от обхват, се извиква нейния унищожител, вътрешният обект се
унищожава, а паметта в купа се освобождава.

Стойностите в кутии може да се достъпят пряко с помощта на оператора звезда –
`*`. Така прескачаме един слой *опосредстване*[^indirection].

```rust,editable
use std::mem;

#[allow(dead_code)]
#[derive(Debug, Clone, Copy)]
struct Point {
    x: f64,
    y: f64,
}

// Може да определим един четирикълник по това къде в пространството се намират
// горният му ляв и долен десен ъгъл.
#[allow(dead_code)]
struct Rectangle {
    top_left: Point,
    bottom_right: Point,
}

fn origin() -> Point {
    Point { x: 0.0, y: 0.0 }
}

fn boxed_origin() -> Box<Point> {
    // Заделяме място за точката на купа и връщаме указател към нея.
    Box::new(Point { x: 0.0, y: 0.0 })
}

fn main() {
    // (всички отбелязвания за тип са излишни)
    // Променлива сложена на кама̀рата
    let point: Point = origin();
    let rectangle: Rectangle = Rectangle {
        top_left: origin(),
        bottom_right: Point { x: 3.0, y: -4.0 }
    };

    // Променлива сложена в купа̀
    let boxed_rectangle: Box<Rectangle> = Box::new(Rectangle {
        top_left: origin(),
        bottom_right: Point { x: 3.0, y: -4.0 },
    });

    // Изходът от функции може да се сложи в кутии
    let boxed_point: Box<Point> = Box::new(origin());

    // Двойно опосредстване
    let box_in_a_box: Box<Box<Point>> = Box::new(boxed_origin());

    println!("Точката заема {} байта на кама̀рата",
             mem::size_of_val(&point));
    println!("Четиръгълникът заема {} байта на кама̀рата",
             mem::size_of_val(&rectangle));

    // box size == pointer size
    println!("Точката в кутия заема {} байта на кама̀рата",
             mem::size_of_val(&boxed_point));
    println!("Четиръгълникът в кутия заема {} байта на кама̀рата",
             mem::size_of_val(&boxed_rectangle));
    println!("Кутията в кутия заема {} байта на кама̀рата",
             mem::size_of_val(&box_in_a_box));

    // Копираме данните, съдържащи се в `boxed_point` в `unboxed_point`
    let unboxed_point: Point = *boxed_point;
    println!("Точката без кутия заема {} байта на кама̀рата",
             mem::size_of_val(&unboxed_point));
}
```

## Б. пр.

[^indirection]: опосредстване – indirection

### Вижте също

[ПРИ][raii]

[raii]: ../scope/raii.md

