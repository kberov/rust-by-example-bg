# Частични премествания

При [разлагането][destructuring] на отделна променлива чрез преместване и като
препратка може да се ползва [обвързване (на променливи) по
образец][^pattern_binding]. От това може да последва _частично преместване_ на
променливата, което означава, че част от променливата е преместена, а друга не
е. В този случай изходната шроменлива не може да бъде използвана като цяло.
Само частите, които са присвоени на нови променливи като препратки, могат да
бъдат използвани. 

```rust,editable
fn main() {
    #[derive(Debug)]
    struct Person {
        name: String,
        age: Box<u8>,
    }

    let person = Person {
        name: String::from("Alice"),
        age: Box::new(20),
    };

    // Стойността, присвоена на променливата `name` е преместена извън person,
    // но `age` е само препратка.
    let Person { name, ref age } = person;

    println!("The person's age is {}", age);

    println!("The person's name is {}", name);

    // Грешка! заемане на частично преместена стойност: `person` случва се
    // частично преместване
    //println!("The person struct is {:?}", person);

    // `person` не може да се ползва, но `person.age`, понеже нейната стойност
    // не е преместена
    println!("The person's age from person struct is {}", person.age);
}
```
(In this example, we store the `age` variable on the heap to 
illustrate the partial move: deleting `ref` in the above code would 
give an error as the ownership of `person.age` would be moved to the 
variable `age`. If `Person.age` were stored on the stack, `ref` would 
not be required as the definition of `age` would copy the data from 
`person.age` without moving it.)


## Б.пр.

[^pattern_binding] обвързване по образец (при разлагане на структура например) – pattern bindings.

### See also:
[Разлагане][destructuring]

[destructuring]: ../../flow_control/match/destructuring.md
