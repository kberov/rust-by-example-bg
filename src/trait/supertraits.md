# Надотличители

Ръждьо няма "наследяване", но можете да опишете отличител, който да е
надмножество[^supertrait] на друг отличител. Например:

```rust,editable
trait Person {
    fn name(&self) -> String;
}

// Person  е надотличител на Student.
// Осъществяването на Student изисква да осъществите също и методите в Person.
trait Student: Person {
    fn university(&self) -> String;
}

trait Programmer {
    fn fav_language(&self) -> String;
}

// CompSciStudent (студент по компютърни науки) е подотличител едновременно на
// Programmer и Student. Осъществяването на CompSciStudent изисква да осъществите
// и двата Надотличителя.
trait CompSciStudent: Programmer + Student {
    fn git_username(&self) -> String;
}

fn comp_sci_student_greeting(student: &dyn CompSciStudent) -> String {
    format!(
        "My name is {} and I attend {}.
        My favorite language is {}. My Git username is {}",
        student.name(),
        student.university(),
        student.fav_language(),
        student.git_username()
    )
}

fn main() {}
```

## Б.пр.

[^supertrait]: надотличител – supertrait. Подобно на „надклас” в други езици
  „надотличител”-ят е надмножество на друг отличител. А всяка
  структура може да осъществява или да бъде производна на повече от един
  отличител.


### Вижте също:

[The Ръждьо Programming Language chapter on supertraits][trpl_supertraits]

[trpl_supertraits]: https://doc.rust-lang.org/book/ch19-03-advanced-traits.html#using-supertraits-to-require-one-traits-functionality-within-another-trait
