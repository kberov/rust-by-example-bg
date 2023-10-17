# ПИИ

Променливите в Ръждьо не са просто имена за данни, подредени на
*стълп*[^stack]: Те също така *владеят* части от паметта, например `Box<T>`
владее памет в *купа*[^heap]. Ръждьо спазва принципа ПИИ (Придобиването на
Източник[^resource] е Инициализация) – [RAII][raii] (Resource Acquisition Is
Initialization). Това означава, че винаги, когато някой обект излезе от обхват,
неговият метод за унищожаване се извиква и владените от него ресурси се
освобождават.

Това поведение предпазва от бъгове, причинени от *утечки на
ресурси*[^res_leaks]. Повече никога няма да ви се наложи да освобождавате памет
на ръка или да се притеснявате за утечки в паметта. Ето показно̀:

```rust,editable
// raii.rs
fn create_box() {
    // Заделяме място за цяло число на купа.
    let _box1 = Box::new(3i32);

    // `_box1` се унищожава тук и паметта се освобождава
}

fn main() {
    // Заделяме място за цяло число на купа.
    let _box2 = Box::new(5i32);

    // Вложен блок (и обхват):
    {
        // Заделяме място за цяло число на купа.
        let _box3 = Box::new(4i32);

        // `_box3` се унищожава тук и паметта се освобождава 
    }

    // Създаване много кутии за забавление
    // Не се налага да освобождаваме памет на ръка!
    for _ in 0u32..1_000 {
        create_box();
    }

    // `_box2` се унищожава тук и паметта се освобождава 
}
```

Разбира се, винаги можем да проверим за грешки с помощта на [`valgrind`][valgrind]:

<!-- REUSE-IgnoreStart -->
<!-- Prevent REUSE from parsing the copyright изявлениe in the sample code -->
```shell
$ rustc raii.rs && valgrind ./raii
==26873== Memcheck, a memory error detector
==26873== Copyright (C) 2002-2013, and GNU GPL'd, by Julian Seward et al.
==26873== Using Valgrind-3.9.0 and LibVEX; rerun with -h for copyright info
==26873== Command: ./raii
==26873==
==26873==
==26873== HEAP SUMMARY:
==26873==     in use at exit: 0 bytes in 0 blocks
==26873==   total heap usage: 1,013 allocs, 1,013 frees, 8,696 bytes allocated
==26873==
==26873== All heap blocks were freed -- no leaks are possible
==26873==
==26873== For counts of detected and suppressed errors, rerun with: -v
==26873== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 2 from 2)
```
<!-- REUSE-IgnoreEnd -->

Няма утечки!

## Унищожител

Идеята за *унищожител*[^destructor] в Ръждьо е осъществена от отличителя
[`Drop`]. Унищожителят се извиква, когато ресурсът излезе от обхват. Не е
задължително този отличител да се осъществява за всеки тип, а само когато
вашият тип изсква да се направи нещо при унщожаване на обекта.

Изпълнете следващия пример, за да видите как работи [`Drop`]. Когато
променливата във функцията `main` излезе от обхват, потребителският унищожител
ще бъде извикан.

```rust,editable
struct ToDrop;

impl Drop for ToDrop {
    fn drop(&mut self) {
        println!("ToDrop is being dropped");
    }
}

fn main() {
    let x = ToDrop;
    println!("Made a ToDrop!");
}
```
## Б. пр.

[^stack]: стълп, стек – stack. Подредена памет за временно съхранение в
  сметалото. Там съхраняваните единици са подредени условно казано отдолу
  нагоре и може да се вземат само отгоре – последната влязла стойност излиза
  първа. Източник: БТР, София 1996, Наука и Изкуство. [Вижте Стек в
  Уикипедия.](https://bg.wikipedia.org/wiki/Стек).

[^heap]: куп – heap. Другият вид памет на програмата – не подреден, различен
  от стълпа, който е подреден.

[^resource]: източник – resource 

[^res_leaks]: утечки на източници/ресурси (в паметта) – resource (memory) leaks 

[^destructor]: унищожител – destructor.

### Вижте също:

[Box][box]

[raii]: https://en.wikipedia.org/wiki/Resource_Acquisition_Is_Initialization
[box]: ../std/box.md
[valgrind]: http://valgrind.org/info/
[`Drop`]: https://doc.rust-lang.org/std/ops/trait.Drop.html
