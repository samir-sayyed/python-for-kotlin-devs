# 🐍 Python for Kotlin Developers

> You already think in types, null-safety, coroutines, and data classes.
> Python will feel like someone removed the guardrails — then handed you a jetpack.
> This guide translates what you *already know* instead of teaching you to code from zero.

**How to read this:** every concept is Kotlin on the left, Python on the right. Same idea, different dialect. Skim the code, read the ⚡ **gotcha** lines, steal the cheat sheet at the bottom.

```kotlin
// Kotlin                                # Python
val name = "Samir"                       name = "Samir"
fun greet() = println("Hi, $name")       def greet(): print(f"Hi, {name}")
```

---

## 🎮 Play & read it in the browser

This repo ships two interactive pages so you can learn while having fun:

- **[🎮 Translation Game](https://samir-sayyed.github.io/python-for-kotlin-devs/quiz.html)** — 16 rounds: see a Kotlin snippet, pick the idiomatic Python. Score, streaks, 3 lives, and the gotcha explained after every answer.
- **[🏠 Landing hub](https://samir-sayyed.github.io/python-for-kotlin-devs/)** — jump into the game or the guide.

**Run it locally** (no build step, no dependencies):

```bash
git clone https://github.com/samir-sayyed/python-for-kotlin-devs.git
cd python-for-kotlin-devs
python3 -m http.server 8000
# then open http://localhost:8000 in your browser
```

The pages are plain self-contained `index.html` / `quiz.html` — open them straight from disk too, or edit the `QUESTIONS` array in `quiz.html` to add your own rounds.

---

## The one-paragraph culture shock

Kotlin protects you at **compile time**. Python protects you at **runtime, if at all**. There is no compiler yelling before you run. `null` isn't a special citizen — it's just `None`, an ordinary object. Types are hints, not laws. This feels terrifying for a week, then liberating: no build step, a REPL that runs anything, and libraries for everything. The trade you're making is **safety for speed-of-iteration**. Learn to lean on tests and type hints to buy back the safety you gave up.

---

## Table of contents

1. [Variables & the missing `val`/`var`](#1-variables--the-missing-valvar)
2. [No null safety — meet `None`](#2-no-null-safety--meet-none)
3. [Functions & default args](#3-functions--default-args)
4. [Strings & f-strings](#4-strings--f-strings)
5. [Collections](#5-collections)
6. [`when` → `match` & the `if` expression gap](#6-when--match--the-if-expression-gap)
7. [Data classes](#7-data-classes)
8. [Classes, `init`, and `self`](#8-classes-init-and-self)
9. [Null-safe calls & the `?.` you'll miss](#9-null-safe-calls--the--youll-miss)
10. [Scope functions (`let`, `apply`, `also`)](#10-scope-functions-let-apply-also)
11. [Lambdas & higher-order functions](#11-lambdas--higher-order-functions)
12. [Sequences → generators (lazy eval)](#12-sequences--generators-lazy-eval)
13. [Coroutines → async/await](#13-coroutines--asyncawait)
14. [Exceptions](#14-exceptions)
15. [Extension functions (and why Python shrugs)](#15-extension-functions-and-why-python-shrugs)
16. [Type hints — Kotlin types in disguise](#16-type-hints--kotlin-types-in-disguise)
17. [Packages, imports & the `main` idiom](#17-packages-imports--the-main-idiom)
18. [Idiomatic cheat sheet](#-idiomatic-cheat-sheet)
19. [Kotlin habit → Python reality](#-kotlin-habit--python-reality)
20. [What you gain / what you lose](#-what-you-gain--what-you-lose)
21. [7-day learning plan](#-7-day-learning-plan)

---

## 1. Variables & the missing `val`/`var`

```kotlin
// Kotlin                                # Python
val x = 5      // immutable              x = 5      # everything is reassignable
var y = 10     // mutable                y = 10     # no val/var distinction
y = 20                                   y = 20
```

⚡ **Gotcha:** Python has no `val`. *Nothing* is truly immutable at the variable level. Want a constant? Convention is `SCREAMING_CASE` and a promise not to touch it. The compiler won't stop you — your teammates will.

```python
MAX_RETRIES = 3   # "constant" by convention, not enforcement
```

---

## 2. No null safety — meet `None`

This is the biggest mental shift. In Kotlin `null` is quarantined by the type system. In Python, `None` is just a value that can show up *anywhere*.

```kotlin
// Kotlin                                # Python
val name: String? = null                 name = None
val len = name?.length ?: 0              length = len(name) if name is not None else 0
```

⚡ **Gotcha:** There is no `String?` vs `String`. Any variable can be `None`. The `?.` and `?:` operators don't exist. You defend with `if x is not None` and (later) type hints like `Optional[str]`.

```python
# Always compare None with `is`, never ==
if value is None:
    ...
```

---

## 3. Functions & default args

```kotlin
// Kotlin                                          # Python
fun add(a: Int, b: Int = 0): Int = a + b           def add(a, b=0):
                                                       return a + b

add(1)          // 1                                add(1)            # 1
add(1, b = 2)   // 3                                add(1, b=2)       # 3  (named args just work)
```

⚡ **Gotcha:** **Never use a mutable default argument.** `def f(items=[])` creates *one* list shared across all calls. This is the #1 Python footgun for newcomers.

```python
def f(items=None):          # ✅ the correct idiom
    if items is None:
        items = []
```

---

## 4. Strings & f-strings

```kotlin
// Kotlin                                          # Python
val s = "Hi $name, you are ${age + 1}"             s = f"Hi {name}, you are {age + 1}"
val raw = """multi                                 raw = """multi
line"""                                            line"""
```

f-strings are Kotlin's `$`/`${}` templates — same power, prefix with `f`. Forgetting the `f` gives you the literal `{name}`, a classic bug.

---

## 5. Collections

```kotlin
// Kotlin                                          # Python
listOf(1, 2, 3)                                    [1, 2, 3]              # list
mutableListOf(1, 2)                                [1, 2]                # lists are always mutable
setOf(1, 2)                                        {1, 2}                # set
mapOf("a" to 1)                                    {"a": 1}              # dict

list.map { it * 2 }                                [x * 2 for x in lst]          # comprehension
list.filter { it > 0 }                             [x for x in lst if x > 0]
list.first { it > 0 }                              next(x for x in lst if x > 0)
```

⚡ **Gotcha:** Kotlin's `.map {}`/`.filter {}` chains become **comprehensions**, read right-to-left-ish: `[expr for item in iterable if cond]`. There's no `it` — you name the loop variable. `map()`/`filter()` functions exist but comprehensions are idiomatic.

---

## 6. `when` → `match` & the `if` expression gap

```kotlin
// Kotlin                                          # Python (3.10+)
val r = when (x) {                                 match x:
    1 -> "one"                                         case 1: r = "one"
    2 -> "two"                                          case 2: r = "two"
    else -> "many"                                     case _: r = "many"
}
```

⚡ **Gotcha:** In Kotlin `if`/`when` are **expressions** that return a value. In Python, `if`/`match` are **statements** — they don't return anything. For the one-liner ternary you reach for a different syntax:

```python
r = "positive" if x > 0 else "non-positive"   # Kotlin: if (x > 0) "positive" else "..."
```

`match` (3.10+) is newer and does real structural pattern matching, but plain `if/elif/else` is still the everyday tool.

---

## 7. Data classes

Your beloved `data class` has a direct heir: `@dataclass`.

```kotlin
// Kotlin                                          # Python
data class User(                                   from dataclasses import dataclass
    val name: String,
    val age: Int = 0                               @dataclass
)                                                  class User:
                                                       name: str
                                                       age: int = 0
```

You get `__init__`, `__repr__`, and `__eq__` for free — exactly like Kotlin generates `equals`/`hashCode`/`toString`/`copy`. Frozen (immutable) version:

```python
@dataclass(frozen=True)   # ~ Kotlin's val-only data class
class Point:
    x: int
    y: int
```

---

## 8. Classes, `init`, and `self`

```kotlin
// Kotlin                                          # Python
class Counter(start: Int) {                        class Counter:
    var count = start                                  def __init__(self, start):
    fun inc() { count++ }                                  self.count = start
}                                                      def inc(self):
                                                           self.count += 1
```

⚡ **Gotcha:** `self` is Kotlin's `this`, **but you must write it explicitly** — as the first parameter of every method *and* every time you touch a field (`self.count`, not `count`). Forgetting `self.` is the most common Kotlin-dev mistake. There's no `init {}` block; the constructor *is* `__init__`.

---

## 9. Null-safe calls & the `?.` you'll miss

You will genuinely miss `?.` and `?:`. Here's how to cope:

```kotlin
// Kotlin                                          # Python
user?.address?.city                                user.address.city if user and user.address else None
name ?: "default"                                  name or "default"        # ⚠️ see gotcha
map["key"] ?: 0                                    my_dict.get("key", 0)    # .get() with default
```

⚡ **Gotcha:** `x or "default"` looks like `?:` but fires on *any* falsy value — `0`, `""`, `[]`, `False` — not just `None`. If you specifically mean "when None", write it explicitly: `x if x is not None else "default"`.

---

## 10. Scope functions (`let`, `apply`, `also`)

Python has no `let`/`apply`/`run`/`also`/`with`-scope-functions. Mostly you just... use a variable. Kotlin's scoping sugar was solving a null-safety problem Python doesn't frame the same way.

```kotlin
// Kotlin                                          # Python
val len = value?.let { it.length }                 length = len(value) if value is not None else None

config.apply {                                     # just set attributes directly
    host = "localhost"                             config.host = "localhost"
    port = 8080                                    config.port = 8080
}
```

⚡ **Takeaway:** Don't hunt for an equivalent. The "one true way" in Python is boring, explicit code. That *is* the idiom.

---

## 11. Lambdas & higher-order functions

```kotlin
// Kotlin                                          # Python
val square = { x: Int -> x * x }                   square = lambda x: x * x
list.sortedBy { it.age }                           sorted(lst, key=lambda u: u.age)
list.forEach { println(it) }                       for x in lst: print(x)
```

⚡ **Gotcha:** Python `lambda`s are **one expression only** — no multi-line bodies, no statements inside. Need more than one line? Define a real `def`. This feels restrictive coming from Kotlin's block lambdas, and it's intentional — Python nudges you toward named functions.

---

## 12. Sequences → generators (lazy eval)

Kotlin's `.asSequence()` lazy chains map beautifully to Python **generators**.

```kotlin
// Kotlin                                          # Python
sequenceOf(1, 2, 3)                                (x for x in [1, 2, 3])       # generator expr
    .map { it * 2 }                                (x * 2 for x in data)        # lazy, nothing runs yet
    .filter { it > 2 }
    .toList()                                      list(gen)                    # force evaluation

fun nums(): Sequence<Int> = sequence {             def nums():
    yield(1); yield(2)                                 yield 1
}                                                      yield 2
```

⚡ **Mental model:** `()` instead of `[]` around a comprehension makes it lazy. `yield` is `yield`. Nothing computes until you iterate — same deal as Kotlin sequences.

---

## 13. Coroutines → async/await

You know structured concurrency. Python's `asyncio` is the same shape, different keywords.

```kotlin
// Kotlin                                          # Python
suspend fun fetch(): String { ... }                async def fetch() -> str: ...

coroutineScope {                                   import asyncio
    val a = async { fetch() }                      async def main():
    val b = async { fetch() }                          a, b = await asyncio.gather(fetch(), fetch())
    a.await() + b.await()
}

runBlocking { main() }                             asyncio.run(main())
```

| Kotlin | Python |
|---|---|
| `suspend fun` | `async def` |
| `.await()` | `await` |
| `async { }` + `awaitAll` | `asyncio.gather(...)` |
| `launch { }` | `asyncio.create_task(...)` |
| `Dispatchers.IO` | (single event loop; use threads/processes for CPU) |
| `runBlocking` | `asyncio.run(...)` |

⚡ **Gotcha:** Python has **one event loop**, and the infamous **GIL** means threads don't give you parallel CPU. For CPU-bound work you use `multiprocessing`, not more coroutines. `async` in Python is for I/O concurrency, full stop.

---

## 14. Exceptions

```kotlin
// Kotlin                                          # Python
try {                                              try:
    risky()                                            risky()
} catch (e: IOException) {                         except IOError as e:
    handle(e)                                          handle(e)
} finally {                                         finally:
    cleanup()                                          cleanup()
}
```

⚡ **Gotcha:** Python has **no checked exceptions** and, culturally, **EAFP** — "Easier to Ask Forgiveness than Permission." Kotlin says "check first" (`if (map.contains(k))`). Python says "just try it and catch":

```python
try:
    value = my_dict[key]
except KeyError:
    value = default
```

This is idiomatic Python, not a code smell.

---

## 15. Extension functions (and why Python shrugs)

Kotlin's killer feature — adding methods to types you don't own — has no clean equivalent. Python's answer is usually **a plain function**.

```kotlin
// Kotlin                                          # Python
fun String.shout() = uppercase() + "!"             def shout(s):
"hi".shout()                                            return s.upper() + "!"
                                                   shout("hi")
```

⚡ **Takeaway:** Python culture prefers `shout(x)` over `x.shout()`. Monkey-patching real classes is possible but frowned on. Let go of the fluent extension habit; module-level functions are fine and normal here.

---

## 16. Type hints — Kotlin types in disguise

Good news: you can bring your types back. They're optional and unenforced at runtime, but tools like **mypy**/**pyright** give you Kotlin-grade checking.

```kotlin
// Kotlin                                          # Python
fun greet(name: String): String                    def greet(name: str) -> str:
val ages: List<Int>                                 ages: list[int]
val user: User?                                     user: User | None      # or Optional[User]
val m: Map<String, Int>                             m: dict[str, int]
```

⚡ **Advice for a Kotlin dev:** **use type hints from day one.** They make Python feel like home, catch the null bugs you're used to catching, and your IDE lights up. Run `mypy` in CI and you've bought back most of the safety you gave up in section 2.

---

## 17. Packages, imports & the `main` idiom

```kotlin
// Kotlin                                          # Python
import kotlin.math.sqrt                             from math import sqrt
package com.samir.app                               # dir with __init__.py = package

fun main() { ... }                                 def main(): ...

                                                   if __name__ == "__main__":
                                                       main()
```

⚡ **Gotcha:** That `if __name__ == "__main__":` line is Python's `fun main()`. It means "run this only when the file is executed directly, not when imported." Every runnable script ends with it. It looks weird; you'll type it in your sleep within a week.

---

## 🧾 Idiomatic cheat sheet

| Kotlin | Python |
|---|---|
| `val x = 5` | `x = 5` |
| `var y = 5` | `y = 5` |
| `null` | `None` |
| `x?.y` | `x.y if x else None` |
| `x ?: d` | `x if x is not None else d` |
| `"$a and $b"` | `f"{a} and {b}"` |
| `listOf(1,2)` | `[1, 2]` |
| `mapOf("a" to 1)` | `{"a": 1}` |
| `list.map { it*2 }` | `[i*2 for i in list]` |
| `list.filter { it>0 }` | `[i for i in list if i>0]` |
| `list.first { pred }` | `next(i for i in list if pred)` |
| `when(x){...}` | `match x: case ...` / `if/elif` |
| `if(c) a else b` | `a if c else b` |
| `data class` | `@dataclass` |
| `this` | `self` (explicit!) |
| `init {}` | `def __init__(self):` |
| `x.let{}` | just use a variable |
| `{ x -> x*x }` | `lambda x: x*x` |
| `.asSequence()` | `(x for x in ...)` generator |
| `yield()` | `yield` |
| `suspend fun` | `async def` |
| `.await()` | `await` |
| `awaitAll` | `asyncio.gather()` |
| `runBlocking{}` | `asyncio.run()` |
| `try/catch/finally` | `try/except/finally` |
| `fun String.ext()` | `def ext(s):` (plain fn) |
| `List<Int>` | `list[int]` |
| `Int?` | `int \| None` |
| `import a.b.c` | `from a.b import c` |
| `fun main()` | `if __name__ == "__main__":` |

---

## 🔁 Kotlin habit → Python reality

| Your Kotlin habit | Python reality |
|---|---|
| Compiler catches my mistakes | Nothing catches them until runtime — **write tests + use mypy** |
| `null` is special & tracked | `None` is an ordinary value, anywhere |
| `val` means immutable | Nothing is immutable; convention only |
| `?.` everywhere | Explicit `if x is not None` |
| Fluent `.map{}.filter{}` chains | Comprehensions `[f(x) for x in xs if p(x)]` |
| Extension functions | Plain module-level functions |
| `if`/`when` return values | They're statements; use the `a if c else b` ternary |
| Scope functions (`let`/`apply`) | Boring explicit code — that's the idiom |
| Check-then-act (LBYL) | Try-then-catch (EAFP) |
| `this` is implicit | `self` is explicit, always |
| Threads = parallelism | GIL: use `multiprocessing` for CPU work |

---

## ⚖️ What you gain / what you lose

**You gain**
- 🚀 No build step. Edit, run, repeat. A REPL that executes anything instantly.
- 📚 The deepest library ecosystem on earth (data, ML, scripting, web, glue).
- ✍️ Ruthlessly readable syntax — whitespace *is* the block structure.
- 🧪 Fearless prototyping — go from idea to running code in seconds.

**You lose**
- 🛡️ Compile-time safety (buy it back with **type hints + mypy**).
- 🚫 Null tracking (buy it back with `Optional`/`| None` hints).
- 🧵 True thread parallelism (the GIL; reach for `multiprocessing`).
- 🔒 Enforced immutability and `?.`/`?:` ergonomics.

---

## 📅 7-day learning plan

| Day | Focus | Do this |
|---|---|---|
| **1** | Syntax & the REPL | Sections 1–5. Play in the REPL (`python3`). Translate 5 old Kotlin snippets. |
| **2** | Control flow & functions | Sections 3, 6, 11. Rewrite a Kotlin `when` chain as `match` and `if/elif`. |
| **3** | Classes & dataclasses | Sections 7, 8. Port a Kotlin `data class` model to `@dataclass`. |
| **4** | The Pythonic mindset | Sections 9, 10, 14, 15. Practice EAFP; unlearn `?.`. |
| **5** | Type hints | Section 16. Add hints to everything from days 1–4. Run `mypy`. |
| **6** | Lazy & async | Sections 12, 13. Rebuild a coroutine snippet in `asyncio`. |
| **7** | Ship something | Write a real script: read a file, transform data, print a report. End it with `if __name__ == "__main__":`. |

**Daily exercise source:** [exercism.org/tracks/python](https://exercism.org/tracks/python) — coming from Kotlin, its Python track will feel like a warm-up.

---

> **The mantra:** Python trades compile-time safety for iteration speed.
> As a Kotlin dev, your superpower is knowing *exactly* what safety you gave up —
> so bring type hints and tests, and you get the best of both worlds. 🐍✨
