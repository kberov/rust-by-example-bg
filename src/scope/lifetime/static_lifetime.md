# Статичeн

Ръждьо има няколко запазени имена за животи. Едно от тях е `'static`. Може да
ги срещнете в два случая.

```rust, editable
// Препратка със 'static – статична продължителност на живота:
let s: &'static str = "hello world";

// 'static като част от предел за отличител:
fn generic<T>(x: T) where T: 'static {}
```

Двата случая са свързани но има разлика и това често води до объркване при
изучаването на Ръждьо. Ето някои примери за всеки от случаите.

## Като живот на препратка

Препратка със живот, обозначен като `'static`, указва, че данните, към които
сочи препратката, живеят през цялото време, докато работи програмата. Тази
трайност може принудително да бъде съкратена.

Има два начина за създаване на променлива със статичен (`'static`) живот. И в
двата случая данните се съхраняват в статичната (само за четене) памет на
изпълнимия файл.

* Създаване на константа с ключовата дума `static`.
* Създаване на на низова буквална стойност от тип `&'static str`.

В следващия пример са онагледени и двата начина.

```rust,editable
// Създаваме константа със `'static` продължителност на живота.
static NUM: i32 = 18;

// Връща препратка към  `NUM` като нейния `'static` живот е принудително сведен
// до този на входния аргумент.
fn coerce_static<'a>(_: &'a i32) -> &'a i32 {
    &NUM
}

fn main() {
    {
        // Създаваме низова буквална стойност и я отпечатваме:
        let static_string = "Аз съм в паметта само за четене.";
        println!("static_string: {}", static_string);

        // Когато `static_string` излезе от обхват, препратката не може да бъде
        // ползвана повече, но данните си остават в двоичния файл.
    }

    {
        // Създаваме число, за да го подадем на `coerce_static`:
        let lifetime_num = 9;

        // Съкращаваме живота на `NUM` до този на `lifetime_num`:
        let coerced_static = coerce_static(&lifetime_num);

        println!("coerced_static: {}", coerced_static);
    }

    println!("NUM: {} остава достъпно!", NUM);
}
```

## Като предел на отличител

Като предел на отличител `'static` означава, че типът не съдържа никакви
нестатични препратки. Сиреч получателят може да държи типа колкото му е
необходимо и да знае, че данните в този тип винаги ще са дейстивителни, докато
ги изхвърли.

*Неясен смисъл на английски. Д го погледна пак§!*
Важно е да се знае, че това означава, че всички притежавани данни винаги минават проверка за статична (`'static`) продължителност, но….
It's important to understand this means that any owned data always passes
a `'static` lifetime bound, but a reference to that owned data generally
does not:

```rust,editable,compile_fail
use std::fmt::Debug;

fn print_it( input: impl Debug + 'static ) {
    println!( "подадената 'static стойност е: {:?}", input );
}

fn main() {
    // i е собствена и не съдържа препратки, значи е 'static:
    let i = 5;
    print_it(i);

    // Опааа, &i има живот само колкото е обхвата на main(),
    // значи не е 'static:
    print_it(&i);
}
```

Компилаторът ще ви каже:

```ignore
error[E0597]: `i` does not live long enough
  --> src/lib.rs:15:15
   |
15 |     print_it(&i);
   |     ---------^^--
   |     |         |
   |     |         borrowed value does not live long enough
   |     argument requires that `i` is borrowed for `'static`
16 | }
   | - `i` dropped here while still borrowed
```

### See also:

[`'static` constants][static_const]

[static_const]: ../../custom_types/constants.md
