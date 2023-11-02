# Съчетатели: `and_then`

Описахме `map()` като начин за верижно изпълнение и опростяване на изявления
`match`. Но ако използваме `map()` с функция, която връща `Option<T>`,
получаваме вгнезден избор `Option<Option<T>>`. В такива случаи, ако имаме
верижно изпълнение, може да стане объркващо. Тук идва редът на друг съчетател,
познат в някои езици като _плоскосъпоставяне_ (flatmap). Този съчетател се
нарича `and_then()` – `и_после()`. 

`and_then()` извиква подадената ѝ функция с обгърната стойност и връща
резултата. Ако изборът (`Option`) е `None`, тогава връща `None`.

В следващия пример `cookable_v3()` връща `Option<Food>`. 
Ако ползвахме `map()` вместо `and_then()`, щяхме да получим 
`Option<Option<Food>>`, което е неподходящ тип за `eat()`.

```rust,editable
#![allow(dead_code)]

#[derive(Debug)] enum Food { CordonBleu, Steak, Sushi }
#[derive(Debug)] enum Day { Monday, Tuesday, Wednesday }

// Нямаме съставки, за да направим Суши.
fn have_ingredients(food: Food) -> Option<Food> {
    match food {
        Food::Sushi => None,
        _           => Some(food),
    }
}

// Имаме рецепти за всичко освен за Кордон Бльо.
fn have_recipe(food: Food) -> Option<Food> {
    match food {
        Food::CordonBleu => None,
        _                => Some(food),
    }
}

// За да направим ястие, ни трябват и рецептата, и съставките.
// Можем да представим логиката като верига от съвпадения `match`:
fn cookable_v1(food: Food) -> Option<Food> {
    match have_recipe(food) {
        None       => None,
        Some(food) => have_ingredients(food),
    }
}

// Това може да се пренапише много по-сбито с `and_then()`:
fn cookable_v3(food: Food) -> Option<Food> {
    have_recipe(food).and_then(have_ingredients)
}

// Иначе ще трябва да сплескаме (`flatten()`) една `Option<Option<Food>>`,
// за да получим `Option<Food>`:
fn cookable_v2(food: Food) -> Option<Food> {
    have_recipe(food).map(have_ingredients).flatten()
}

fn eat(food: Food, day: Day) {
    match cookable_v3(food) {
        Some(food) => println!("Еха! В {:?} ще ядем {:?}.", day, food),
        None       => println!("О, не. Няма да ядем в {:?}?", day),
    }
}

fn main() {
    let (cordon_bleu, steak, sushi) = (Food::CordonBleu, Food::Steak, Food::Sushi);

    eat(cordon_bleu, Day::Monday);
    eat(steak, Day::Tuesday);
    eat(sushi, Day::Wednesday);
}
```

### Вижте също:

[затваряния][closures], [`Option`][option], [`Option::and_then()`][and_then] и [`Option::flatten()`][flatten]

[closures]: ../../fn/closures.md
[option]: https://doc.rust-lang.org/std/option/enum.Option.html
[and_then]: https://doc.rust-lang.org/std/option/enum.Option.html#method.and_then
[flatten]: https://doc.rust-lang.org/std/option/enum.Option.html#method.flatten
