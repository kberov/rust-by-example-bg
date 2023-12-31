# Връщане на отличители със `dyn` 

Компилаторът на Ръждьо трябва да знае колко място в паметта изисква всеки тип
за връщане от функция. Това означава, че всичките ви функции трябва да връщат
определен тип. За разлика от други езици, ако имате отличител `Animal`, тук не
можете да напишете функция, която връща `Animal`, защото различните ѝ
въплъщения ще изискват различно количество памет.

Все пак това ограничение може лесно да се заобиколи. Вместо да връщат направо
обект от отличител, функциите могат да връщат `Box` (кутия), която _съдържа_
някакво въплъщение на `Animal`. Кутията е просто препратка към някакво място от
паметта в купа[^heap]. Понеже размерът на препратката се знае и компилаторът
може да гарантира, че тя сочи към `Animal`, за който е заделено място в купа,
то нашата функция може да върне отличител.  


Ръждьо гледа да бъде колкото се може по-изричен, когато заделя памет на купа.
Така че, ако функцията ви връща препратка-към-отличител-на-купа, трябва да
напишете пред връщания тип ключовата дума `dyn`.

```rust,editable
struct Sheep {}
struct Cow {}

trait Animal {
    // Обявление на метод
    fn noise(&self) -> &'static str;
}

// Осъществяваме отличителя `Animal` за `Sheep`.
impl Animal for Sheep {
    fn noise(&self) -> &'static str {
        "беееее!"
    }
}

// Осъществяваме отличителя `Animal` за `Cow`.
impl Animal for Cow {
    fn noise(&self) -> &'static str {
        "мууууу!"
    }
}

// Връща някаква структура, която осъществява някакво животно (Animal), но не
// знаем кое точно по време на компилация.
fn random_animal(random_number: f64) -> Box<dyn Animal> {
    if random_number < 0.5 {
        Box::new(Sheep {})
    } else {
        Box::new(Cow {})
    }
}

fn main() {
    let random_number = 0.234;
    let animal = random_animal(random_number);
    println!("Избрахте случайно животно и то казва {}", animal.noise());
}

```
## Б. пр.

[^heap]: куп – същ. м. р. ед. ч. – the heap memory
