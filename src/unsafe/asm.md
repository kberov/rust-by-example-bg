# Вписан асемблер

Ръждьо предоставя поддръжка за вписване на код на асемблер[^inline_asm] чрез
макроса `asm!`. Може да се ползва за внедряване на написан на ръка код на
асемблер – на същия асемблер като този, създаден от компилатора. Обикновено
това не е необходимо, но може да се случи, когато нужната производителност не
може да се постигне по друг начин. Пряк достъп до хардуера, например код за
ядрото на уредбата, може също да изисква такава функционалност.


> **Важно**: Примерите тук са написани на асемблер за архитектура x86/x86-64,
> но се поддържат и други архитектури.

Вписан асемблер се поддържа за следните архитектури:
- x86 and x86-64
- ARM
- AArch64
- RISC-V

## Основи

Да започнем с възможно най-простия пример:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

unsafe {
    asm!("nop");
}
# }
```

Това ще вмъкне нареждане[^instruction] „NOP“ (no operation) в асемблерния код,
създаден от компилатора. Забележете, че всички извиквания на `asm!` трябва да
са в блок `unsafe`, тъй като те могат да вмъкнат произволни нареждания към
машината и да счупят иначе непроменлив за програмиста код. Нарежданията за
вмъкване се поставят последователно в първия аргумент на макроса `asm!` като
низова буквална стойност.

## Вход и изход

Въвеждането на нареждане, което не прави нищо, е отегчително. Да направим нещо,
което работи с данни:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let x: u64;
unsafe {
    asm!("mov {}, 5", out(reg) x);
}
assert_eq!(x, 5);
# }
```

Това ще запише стойността `5` в променливата `x` от тип `u64`. Вижда се, че
низът, който ползваме за нарежданията, всъщност е низов образец[^template].
Подчинява се на същите правила като [низовете за форматиране][format-syntax].
Аргументите, които се вмъкват в образеца обаче изглеждат малко по-различно.
Първо трябва да укажем дали променливата е вход или изход за вписания асемблер.
В този случай е изход. Обявихме това, като написахме `out`. Също трябва да
укажем в какъв вид регистър асемблерът очаква променливата. В нашия случай ние
я поставяме в произволен регистър за общо ползване, като указваме `reg`.
Компилаторът ще избере подходящия регистър за вмъкване в образеца и ще прочете
променливата оттам, след като вписаният асемблер приключи изпълнението си.

[format-syntax]: https://doc.rust-lang.org/std/fmt/#syntax

Да видим друг пример, който ползва и вход:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let i: u64 = 3;
let o: u64;
unsafe {
    asm!(
        "mov {0}, {1}",
        "add {0}, 5",
        out(reg) o,
        in(reg) i,
    );
}
assert_eq!(o, 8);
# }
```

Това ще добави `5` към входа в променливата `i` и ще запише резултата в
промеливата `o`. Точният начин, по който се прави това, е първо да се копира
стойността от `i` в изхода, а след това да добави `5` към него.

Примерът показва няколко неща:

Първо виждаме, че `asm!` позволява да се подадат няколко аргумента като низов
образец. Всеки от тях се разглежда като отделен ред асемблерен код, сякаш
всички са съединени със знак за нов ред помежду им. Това улеснява форматирането
на кода.

Второ, виждаме че входните данни се обявяват, като се пише `in` вместо `out`.

Трето, можем да указваме пореден номер на аргумента за заместване в образеца
или име, както във всеки низ за форматиране. Това е особено полезно за
образците за вписан асемблер, тъй като аргументите често се ползват повече от
веднъж. За по-сложен код на асемблер това даже се препоръчва, понеже подобрява
четимостта и позволява преподреждане на нарежданията, без да се променя редът на
аргументите.

Можем да усъвършенстваме горния пример, като избегнем нареждането `mov`:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let mut x: u64 = 3;
unsafe {
    asm!("add {0}, 5", inout(reg) x);
}
assert_eq!(x, 8);
# }
```
Виждаме, че се ползва `inout`, за да се укаже, че даден аргумент е
входноизходен. Това е различно от указване на вход и изход поотделно, защото
гарантира, че ще присвои и двата на един и същи регистър.

Възможно е също да се укажат различни променливи за входната и изходната части
на операнда `inout`:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let x: u64 = 3;
let y: u64;
unsafe {
    asm!("add {0}, 5", inout(reg) x => y);
}
assert_eq!(y, 8);
# }
```

## Late output operands

The Ръждьо compiler is conservative with its allocation of operands. It is assumed that an `out`
can be written at any time, and can therefore not share its location with any other argument.
However, to guarantee optimal performance it is important to use as few registers as possible,
so they won't have to be saved and reloaded around the inline assembly block.
To achieve this Ръждьо provides a `lateout` specifier. This can be used on any output that is
written only after all inputs have been consumed.
There is also a `inlateout` variant of this specifier.

Here is an example where `inlateout` *cannot* be used in `release` mode or other optimized cases:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let mut a: u64 = 4;
let b: u64 = 4;
let c: u64 = 4;
unsafe {
    asm!(
        "add {0}, {1}",
        "add {0}, {2}",
        inout(reg) a,
        in(reg) b,
        in(reg) c,
    );
}
assert_eq!(a, 12);
# }
```
Горното би могло да работи добре за неоптимизирани случаи (режим `Debug`), но ако искате оптимизирана производителност (режим `release` или други случаи на оптимизация), може и да не работи.

That is because in optimized cases, the compiler is free to allocate the same register for inputs `b` and `c` since it knows they have the same value. However it must allocate a separate register for `a` since it uses `inout` and not `inlateout`. If `inlateout` was used, then `a` and `c` could be allocated to the same register, in which case the first instruction to overwrite the value of `c` and cause the assembly code to produce the wrong result.

However the following example can use `inlateout` since the output is only modified after all input registers have been read:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let mut a: u64 = 4;
let b: u64 = 4;
unsafe {
    asm!("add {0}, {1}", inlateout(reg) a, in(reg) b);
}
assert_eq!(a, 8);
# }
```

Както се вижда, това парче ассемблер ще работи правилно, ако `a` и `b` са присвоени на един и същи регистър.

## Explicit register operands

Some instructions require that the operands be in a specific register.
Therefore, Ръждьо inline assembly provides some more specific constraint specifiers.
While `reg` is generally available on any architecture, explicit registers are highly architecture specific. E.g. for x86 the general purpose registers `eax`, `ebx`, `ecx`, `edx`, `ebp`, `esi`, and `edi` among others can be addressed by their name.

```rust,no_run
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let cmd = 0xd1;
unsafe {
    asm!("out 0x64, eax", in("eax") cmd);
}
# }
```

In this example we call the `out` instruction to output the content of the `cmd` variable to port `0x64`. Since the `out` instruction only accepts `eax` (and its sub registers) as operand we had to use the `eax` constraint specifier.

> **Note**: unlike other operand types, explicit register operands cannot be used in the template string: you can't use `{}` and should write the register name directly instead. Also, they must appear at the end of the operand list after all other operand types.

Consider this example which uses the x86 `mul` instruction:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

fn mul(a: u64, b: u64) -> u128 {
    let lo: u64;
    let hi: u64;

    unsafe {
        asm!(
            // The x86 mul instruction takes rax as an implicit input and writes
            // the 128-bit result of the multiplication to rax:rdx.
            "mul {}",
            in(reg) a,
            inlateout("rax") b => lo,
            lateout("rdx") hi
        );
    }

    ((hi as u128) << 64) + lo as u128
}
# }
```

This uses the `mul` instruction to multiply two 64-bit inputs with a 128-bit result.
The only explicit operand is a register, that we fill from the variable `a`.
The second operand is implicit, and must be the `rax` register, which we fill from the variable `b`.
The lower 64 bits of the result are stored in `rax` from which we fill the variable `lo`.
The higher 64 bits are stored in `rdx` from which we fill the variable `hi`.

## Clobbered registers

In many cases inline assembly will modify state that is not needed as an output.
Usually this is either because we have to use a scratch register in the assembly or because instructions modify state that we don't need to further examine.
This state is generally referred to as being "clobbered".
We need to tell the compiler about this since it may need to save and restore this state around the inline assembly block.

```rust
use std::arch::asm;

# #[cfg(target_arch = "x86_64")]
fn main() {
    // three entries of four bytes each
    let mut name_buf = [0_u8; 12];
    // String is stored as ascii in ebx, edx, ecx in order
    // Because ebx is reserved, the asm needs to preserve the value of it.
    // So we push and pop it around the main asm.
    // 64 bit mode on 64 bit processors does not allow pushing/popping of
    // 32 bit registers (like ebx), so we have to use the extended rbx register instead.

    unsafe {
        asm!(
            "push rbx",
            "cpuid",
            "mov [rdi], ebx",
            "mov [rdi + 4], edx",
            "mov [rdi + 8], ecx",
            "pop rbx",
            // We use a pointer to an array for storing the values to simplify
            // the Ръждьо code at the cost of a couple more asm instructions
            // This is more explicit with how the asm works however, as opposed
            // to explicit register outputs such as `out("ecx") val`
            // The *pointer itself* is only an input even though it's written behind
            in("rdi") name_buf.as_mut_ptr(),
            // select cpuid 0, also specify eax as clobbered
            inout("eax") 0 => _,
            // cpuid clobbers these registers too
            out("ecx") _,
            out("edx") _,
        );
    }

    let name = core::str::from_utf8(&name_buf).unwrap();
    println!("CPU Manufacturer ID: {}", name);
}

# #[cfg(not(target_arch = "x86_64"))]
# fn main() {}
```

In the example above we use the `cpuid` instruction to read the CPU manufacturer ID.
This instruction writes to `eax` with the maximum supported `cpuid` argument and `ebx`, `edx`, and `ecx` with the CPU manufacturer ID as ASCII bytes in that order.

Even though `eax` is never read we still need to tell the compiler that the register has been modified so that the compiler can save any values that were in these registers before the asm. This is done by declaring it as an output but with `_` instead of a variable name, which indicates that the output value is to be discarded.

This code also works around the limitation that `ebx` is a reserved register by LLVM. That means that LLVM assumes that it has full control over the register and it must be restored to its original state before exiting the asm block, so it cannot be used as an input or output **except** if the compiler uses it to fulfill a general register class (e.g. `in(reg)`). This makes `reg` operands dangerous when using reserved registers as we could unknowingly corrupt our input or output because they share the same register.

To work around this we use `rdi` to store the pointer to the output array, save `ebx` via `push`, read from `ebx` inside the asm block into the array and then restore `ebx` to its original state via `pop`. The `push` and `pop` use the full 64-bit `rbx` version of the register to ensure that the entire register is saved. On 32 bit targets the code would instead use `ebx` in the `push`/`pop`.

This can also be used with a general register class to obtain a scratch register for use inside the asm code:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

// Multiply x by 6 using shifts and adds
let mut x: u64 = 4;
unsafe {
    asm!(
        "mov {tmp}, {x}",
        "shl {tmp}, 1",
        "shl {x}, 2",
        "add {x}, {tmp}",
        x = inout(reg) x,
        tmp = out(reg) _,
    );
}
assert_eq!(x, 4 * 6);
# }
```

## Symbol operands and ABI clobbers

By default, `asm!` assumes that any register not specified as an output will have its contents preserved by the assembly code. The [`clobber_abi`] argument to `asm!` tells the compiler to automatically insert the necessary clobber operands according to the given calling convention ABI: any register which is not fully preserved in that ABI will be treated as clobbered.  Multiple `clobber_abi` arguments may be provided and all clobbers from all specified ABIs will be inserted.

[`clobber_abi`]: ../../reference/inline-assembly.html#abi-clobbers

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

extern "C" fn foo(arg: i32) -> i32 {
    println!("arg = {}", arg);
    arg * 2
}

fn call_foo(arg: i32) -> i32 {
    unsafe {
        let result;
        asm!(
            "call {}",
            // Function pointer to call
            in(reg) foo,
            // 1st argument in rdi
            in("rdi") arg,
            // Return value in rax
            out("rax") result,
            // Mark all registers which are not preserved by the "C" calling
            // convention as clobbered.
            clobber_abi("C"),
        );
        result
    }
}
# }
```

## Register template modifiers

In some cases, fine control is needed over the way a register name is formatted when inserted into the template string. This is needed when an architecture's assembly language has several names for the same register, each typically being a "view" over a subset of the register (e.g. the low 32 bits of a 64-bit register).

By default the compiler will always choose the name that refers to the full register size (e.g. `rax` on x86-64, `eax` on x86, etc).

This default can be overridden by using modifiers on the template string operands, just like you would with format strings:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let mut x: u16 = 0xab;

unsafe {
    asm!("mov {0:h}, {0:l}", inout(reg_abcd) x);
}

assert_eq!(x, 0xabab);
# }
```

In this example, we use the `reg_abcd` register class to restrict the register allocator to the 4 legacy x86 registers (`ax`, `bx`, `cx`, `dx`) of which the first two bytes can be addressed independently.

Let us assume that the register allocator has chosen to allocate `x` in the `ax` register.
The `h` modifier will emit the register name for the high byte of that register and the `l` modifier will emit the register name for the low byte. The asm code will therefore be expanded as `mov ah, al` which copies the low byte of the value into the high byte.

If you use a smaller data type (e.g. `u16`) with an operand and forget to use template modifiers, the compiler will emit a warning and suggest the correct modifier to use.

## Memory address operands

Sometimes assembly instructions require operands passed via memory addresses/memory locations.
You have to manually use the memory address syntax specified by the target architecture.
For example, on x86/x86_64 using Intel assembly syntax, you should wrap inputs/outputs in `[]` to indicate they are memory operands:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

fn load_fpu_control_word(control: u16) {
    unsafe {
        asm!("fldcw [{}]", in(reg) &control, options(nostack));
    }
}
# }
```

## Labels

Any reuse of a named label, local or otherwise, can result in an assembler or linker error or may cause other strange behavior. Reuse of a named label can happen in a variety of ways including:

-   explicitly: using a label more than once in one `asm!` block, or multiple times across blocks.
-   implicitly via inlining: the compiler is allowed to instantiate multiple copies of an `asm!` block, for example when the function containing it is inlined in multiple places.
-   implicitly via LTO: LTO can cause code from *other crates* to be placed in the same codegen unit, and so could bring in arbitrary labels.

As a consequence, you should only use GNU assembler **numeric** [local labels] inside inline assembly code. Defining symbols in assembly code may lead to assembler and/or linker errors due to duplicate symbol definitions.

Moreover, on x86 when using the default Intel syntax, due to [an LLVM bug], you shouldn't use labels exclusively made of `0` and `1` digits, e.g. `0`, `11` or `101010`, as they may end up being interpreted as binary values. Using `options(att_syntax)` will avoid any ambiguity, but that affects the syntax of the _entire_ `asm!` block. (See [Options](#options), below, for more on `options`.)

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let mut a = 0;
unsafe {
    asm!(
        "mov {0}, 10",
        "2:",
        "sub {0}, 1",
        "cmp {0}, 3",
        "jle 2f",
        "jmp 2b",
        "2:",
        "add {0}, 2",
        out(reg) a
    );
}
assert_eq!(a, 5);
# }
```

This will decrement the `{0}` register value from 10 to 3, then add 2 and store it in `a`.

This example shows a few things:

- First, that the same number can be used as a label multiple times in the same inline block.
- Second, that when a numeric label is used as a reference (as an instruction operand, for example), the suffixes “b” (“backward”) or ”f” (“forward”) should be added to the numeric label. It will then refer to the nearest label defined by this number in this direction.

[local labels]: https://sourceware.org/binutils/docs/as/Symbol-Names.html#Local-Labels
[an LLVM bug]: https://bugs.llvm.org/show_bug.cgi?id=36144

## Options

By default, an inline assembly block is treated the same way as an external FFI function call with a custom calling convention: it may read/write memory, have observable side effects, etc. However, in many cases it is desirable to give the compiler more information about what the assembly code is actually doing so that it can optimize better.

Let's take our previous example of an `add` instruction:

```rust
# #[cfg(target_arch = "x86_64")] {
use std::arch::asm;

let mut a: u64 = 4;
let b: u64 = 4;
unsafe {
    asm!(
        "add {0}, {1}",
        inlateout(reg) a, in(reg) b,
        options(pure, nomem, nostack),
    );
}
assert_eq!(a, 8);
# }
```

Options can be provided as an optional final argument to the `asm!` macro. We specified three options here:
- `pure` means that the asm code has no observable side effects and that its output depends only on its inputs. This allows the compiler optimizer to call the inline asm fewer times or even eliminate it entirely.
- `nomem` means that the asm code does not read or write to memory. By default the compiler will assume that inline assembly can read or write any memory address that is accessible to it (e.g. through a pointer passed as an operand, or a global).
- `nostack` means that the asm code does not push any data onto the stack. This allows the compiler to use optimizations such as the stack red zone on x86-64 to avoid stack pointer adjustments.

These allow the compiler to better optimize code using `asm!`, for example by eliminating pure `asm!` blocks whose outputs are not needed.

See the [reference](https://doc.rust-lang.org/stable/reference/inline-assembly.html) for the full list of available options and their effects.

## Б. пр.

[^inline_asm]: вписан асемблер – inline assembly

[^instruction]: нареждане – instruction

[^template]: низов образец – template string
