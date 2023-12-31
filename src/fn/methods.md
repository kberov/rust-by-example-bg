# Придадени функции и методи

Някои функции се отнасят до определен тип. Те биват два вида: *придадени
функции* и *методи*. Придадените функции са такива, които са обявени изобщо за
типа, докато методите са функции, които се извикват с отделен обект, създаден
от този тип.

```rust,editable
struct Point {
    x: f64,
    y: f64,
}

// Блок за осъществяване. Всички придадени към структурата `Point` функции и
// методите ѝ влизат тук
impl Point {
    // Това е „придадена функция“, защото се отнася до Point.
    //
    // Придадените функции не се извикват задължително с обекти.
    // Тези функции се използват обичайно като конструктори.
    fn origin() -> Point {
        Point { x: 0.0, y: 0.0 }
    }

    // Друга придадена функция, приемаща два аргумента:
    fn new(x: f64, y: f64) -> Point {
        Point { x: x, y: y }
    }
}

struct Rectangle {
    p1: Point,
    p2: Point,
}

impl Rectangle {
    // Това е метод
    // `&self` е краткопис за `self: &Self`, където `Self` е прякор за типа на
    // извикващия обект. В този случай `Self` = `Rectangle`
    fn area(&self) -> f64 {
        // `self` предоставя достъп до полетата на структурата
        // чрез оператора точка (.).
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

        // `abs` е метод на типа `f64`, връщащ абсолютната стойност на
        // извикващия
        ((x1 - x2) * (y1 - y2)).abs()
    }

    fn perimeter(&self) -> f64 {
        // Разлагаме self.p1 и self.p2 в Point
        let Point { x: x1, y: y1 } = self.p1;
        let Point { x: x2, y: y2 } = self.p2;

        2.0 * ((x1 - x2).abs() + (y1 - y2).abs())
    }

    // Този метод изисква извикващият обект да бъде менѝм
    // Дългото изписване на `&mut self` е `self: &mut Self`
    fn translate(&mut self, x: f64, y: f64) {
        self.p1.x += x;
        self.p2.x += x;

        self.p1.y += y;
        self.p2.y += y;
    }
}

// `Pair` владее данни: две цели числа, намиращи се в динамичната памет
struct Pair(Box<i32>, Box<i32>);

impl Pair {
    // Този метод *поглъща* данните на извикващия обект
    // Дългото изписване на `self` е `self: Self`
    fn destroy(self) {
        // Разлагаме `self`
        let Pair(first, second) = self;

        println!("Pair({}, {}) бива разрушен…", first, second);

        // `first` и `second` излизат от обхват и паметта, която са заемали,
        // бива освободена.
    }
}

fn main() {
    let rectangle = Rectangle {
        // Придадените функции се извикват с двойно двоеточие
        p1: Point::origin(),
        p2: Point::new(3.0, 4.0),
    };

    // Методите се извикват с оператора точка
    // Забележете, че първият аргумент `&self` се подава неявно, сиреч
    // `rectangle.perimeter()` === `Rectangle::perimeter(&rectangle)`
    println!("Rectangle perimeter: {}", rectangle.perimeter());
    println!("Rectangle area: {}", rectangle.area());

    let mut square = Rectangle {
        p1: Point::origin(),
        p2: Point::new(1.0, 1.0),
    };

    // Грешка! `rectangle` е неменѝм, но този метод изисква менѝм обект
    //rectangle.translate(1.0, 0.0);
    // ЗАДАЧА ^ Разкоментирайте този ред

    // Добре! Меними обекти могат да извикват меними методи
    square.translate(1.0, 1.0);

    let pair = Pair(Box::new(1), Box::new(2));

    pair.destroy();

    // Грешка! Предишното извикване на `destroy` *погълна* `pair`
    //pair.destroy();
    // ЗАДАЧА ^ Разкоментирайте този ред
}
```
