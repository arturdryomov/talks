<!-- Tool: Marp 0.0.11 -->

# Kotlin: MAX PAYNE

### Artur Dryomov, Juno Lab

###### [@arturdryomov](https://twitter.com/arturdryomov)

---

![bg original](images/max-two-guns.jpg)

---

# Juno :heart: Kotlin 

## Kotlin :broken_heart: Juno

### Well, only sometimes, so no grudge here

---

# Juno Rider :blue_car:

* Full-Kotlin + RxJava from the start
* 2 years
* Kotlin `1.1-EAP` in production for 6+ months :fire:
* Kotlin `1.2-EAP` right now :fire:
* 99.9 % crash-free
* 2 week sprints
* :five: developers, :two: QA

---

# Juno Rider :blue_car:

* `cloc main/kotlin` — 40 K 
* `cloc test/kotlin` — 38 K
* `cloc androidTest/kotlin` — 12 K

---

# Style :lipstick:

---

# Checkstyle and Static Analysis

## So yeah, forget about it :sparkles:

---

# Specification mumbo-jumbo :triangular_ruler:

* Java AST — Abstract Syntax Tree
    * Only Java
* PSI — Program Structure Interface
    * Only IJ
* UAST — Universal AST 
    * Java and Kotlin
    * Android Lint is migrating to it!

---

# Checkstyle

> How about not mapping it to AST and instead map it to UAST (Universal Abstract Syntax Tree)? That way also adding support for Kotlin, which is an official thing for Android now, can be supported.

> Checkstyle works only with AST as we depend on ANTLR and Java grammar.

---

# Checkstyle and Static Analysis :flashlight:

## Put your IJ config into repository

###### You can somewhat run IJ headless

###### Detekt works with PSI, so take it for a spin

---

# ∞ paths

Let’s build something!

```kotlin
val gameBuilder = StringBuilder()

gameBuilder.append("tic")
gameBuilder.append("tac")
gameBuilder.append("toe")

val game = gameBuilder.toString()
```

---

# ∞ paths

Also, `also`.

```kotlin
val game: String = StringBuilder().also {
    it.append("tic")
    it.append("tac")
    it.append("toe")
}
.toString()
```

---

# ∞ paths

Hmm, `let` it be?

```kotlin
val game: String = StringBuilder().let {
    it.append("tic")
    it.append("tac")
    it.append("toe")

    it.toString()
}
```

---

# ∞ paths

`apply` it!

```kotlin
val game: String = StringBuilder().apply {
    append("tic")
    append("tac")
    append("toe")
}
.toString()
```

---

# ∞ paths

`run` seems nice.

```kotlin
val game: String = StringBuilder().run {
    append("tic")
    append("tac")
    append("toe")

    toString()
}
```

---

# ∞ paths

How about `with`?

```kotlin
val game: String = with(StringBuilder()) {
    append("tic")
    append("tac")
    append("toe")

    toString()
}
```

---

# ∞ paths

* Better brew that coffee for long PRs :coffee:
* `?:` vs. `let` vs. `if (it == null)`

---

# ∞ paths :flashlight:

* `CODE_STYLE.md`
* It does not really matter, just go with it :airplane:

---

![bg original](images/max-jumping.jpg)

---

# Weird Parts :jack_o_lantern:

---

# Destructuring

No joke, actually useful.

```kotlin
val (first, second) = pair
val (first, second, third) = triple

Observable
    .fromCallable { createPair() }
    .subscribe { (first, second) ->
        println("1st is $first, 2nd is $second")
    }
```

---

# Destructuring

Supports `data class` and suggested by IJ.

```kotlin
data class Person(
    firstName: String,
    lastName: String
)

val (firstName, lastName) = person
```

---

# Destructuring

Somebody swaps fields :gun: Compiler is happy! :sparkles:

```kotlin
data class Person(
    lastName: String,
    firstName: String
)

val (firstName, lastName) = person
```

---

# Destructuring :flashlight:

## We just ban it unless `Pair` or `Triple`


---

# `null` safety?

This works just fine!

```kotlin
fun createObservable(): Observable<Int> {
    return Observable.just(null)
}
```
:sparkles: OH HAI RxJava 2 :sparkles:

*Reproduced on RxJava 2.1.1.*

---

# `null` safety!!

* The language relies on `@NonNull` annotations.
* Your code is full of unicorns :unicorn:
* Java code is full of `null`.

---

# `null` safety!! :flashlight:

* Read docs and sources carefully.
* Promote `@Nullability` to your favorite libraries.
* Hopefully Android SDK will catch up after announcements.

---

# RxJava 2

* Streams panic on `null` values.
* Kotlin has to be friends with Java.
* Side road — Swift vs. Kotlin :bird:
    * Swift: `?` and `nil` just sugar for `enum Optional`.
    * Kotlin: `?` is a language helper, no guarantees though.

---

# Koptional :flashlight:

## https://github.com/gojuno/koptional

```kotlin
val some = Some(value)
val none = None

val optional = nullableValue.toOptional()
val nullable = o.toNullable()

val valueStream = optionalStream.filterSome()
val signalStream = optionalStream.filterNone()
```
Use `?:` and `let` — embrace the language.

---

# SAM Conversion

*Single Abstract Method*

```kotlin
// RxJava 1
Observable.zip(a, b) { a, b -> a to b}

// RxJava 2
Observable.zip(a, b, BiFunction<Int, Int> { a, b ->
    a to b
})
```

---

# SAM Conversion

```java
// RxJava 1
public static <T1, T2, R> Observable<R> zip
(
    Observable<T1> o1, Observable<T2> o2,
    Func2<T1, T2, R> zipper
)

// RxJava 2
public static <T1, T2, R> Observable<R> zip
(
    ObservableSource<T1> o1, ObservableSource<T2> o2,
    BiFunction<T1, T2, R> zipper
)
```

---

# SAM Conversion

```java
// RxJava 1
public interface Func2<T1, T2, R> extends Function {
    R call(T1 t1, T2 t2);
}

// RxJava 2
public interface BiFunction<T1, T2, R> {
    R apply(T1 t1, T2 t2);
}
```

---

# SAM Conversion

```java
// RxJava 1
public class Observable<T> {
    // Redacted
}


// RxJava 2
public interface ObservableSource<T> {
    void subscribe(Observer<T> observer);
}
```

---

# SAM Conversion

### :star: https://youtrack.jetbrains.com/issue/KT-14984

### *Impossible to pass not all SAM argument as function*

---

# SAM Conversion :flashlight:

## We’ve made a bunch of extensions for now

### RxKotlin has some

---

# Gson and Reflection

```kotlin
data class Model {

    @SerializedName("model_field")
    val field: String

}

model.field.toString() // Crash!
```

---

# Gson and Reflection

## Gson is happy to write `null` to properties

### Reflection is a thing in the Java world...

---

# Gson and Reflection

Fail fast :boom:

```kotlin
data class Model {

    @SerializedName("model_field")
    val field: String

} : Validatable {

    override fun validate() {
        if (field == null) throw ParseException()
    }
}
```

---

# Gson and Reflection :flashlight:

## Take a look at Moshi or Jackson

### Uses Kotlin native reflection :angel:

---

![bg original](images/max-machine-gun.jpg)

---

# Optimizations :rocket:

---

# `inline` or `noinline`

* Neat, reduces methods count.
    * OH HAI Android :sparkles:
* Feels like kernel development.
* Not emraced at all.
* Mostly forces `@Suppress("nothing_to_inline")`.

---

# `inline` or `noinline`

### :star: https://youtrack.jetbrains.com/issue/KT-16769

### *Revisit compiler and IDE warning about inline functions*

---

# `inline` or `noinline` :flashlight:

## Know about it

### I’m looking at you, `private fun`

---

# `companion object` and `const`

```kotlin
// Kotlin
companion object {
    private val NUMBER = 42
}
```
```java
// Java
public static final class Companion {
    private final int NUMBER = 42;
    
    private final int getNUMBER() {
        return NUMBER;
    }
}
```

---

# `companion object` and `const`

* So yeah, +:one: method without `const`.
* Methods will not count themselves!
* Invocation costs are real :clock9:

---

# `companion object` and `const`

```kotlin
// Kotlin
companion object {
    private const val NUMBER = 42
}
```
```java
// Java
public static final class Companion {
    private static final int NUMBER = 42;
    
    // Will actually inline the value.
}
```

---

# `companion object` and `const`

* Not advocated enough.
* Only primitives and `String`.
* Shouldn’t compiler do it itself?

---

# `companion object` and `const` :flashlight:

## Know about it

---

# Hidden Costs :flashlight:

## IJ → Tools → Show Kotlin Bytecode

### Lots of fun, also — scary! :scream: 

---

![bg original](images/max-two-guns-front.jpg)

---

# Tooling :hammer:

---

# Bugs :bug:

```kotlin
fun multiply(double: Double) = double * double
```

Update Kotlin 1.1.2 :arrow_right: 1.1.3.

```text
java.lang.IllegalStateException
org.jetbrains.kotlin.kapt3.diagnostic.KaptError
Error while annotation processing
```
Java keywords are suddenly banned.

*Changelog? What changelog?*

---

# Bugs :bug:

### :star: https://youtrack.jetbrains.com/issue/KT-18377

### *Syntax error while generating kapt stubs*

---

# Bugs :bug:

## Bugs in the language tooling are a thing

---

# Assemble :clock9:

* Coming from Java? +:one: build step.
* Use Dagger? +:one: build step.
* `kapt` incremental compilation didn’t work for a long time...
    * Have a change? `REBUILD`. 
* Just remember you are doing it :100: times per day.

---

# Assemble :clock9:

* Compilation is CPU intensive.
* Laptops have mobile CPUs.
* PCs are cheap, easy to upgrade...

---

# Mainframer :computer:

### https://github.com/gojuno/mainframer
### https://github.com/elpassion/mainframer-intellij-plugin

1. Laptop → *Source Code* → Build Machine.
2. Build Machine → *Build Results* → Laptop.

---

# Mainframer :computer:

```plain
$ ./gradlew clean assembleDebug --no-daemon
```

* Without MF: 4 minutes 10 seconds.
    * Laptop overheat :fire:
    * Basically cannot use your computer :broken_heart:
* With MF: 1 minute 10 seconds.

---

# Assemble :clock9: :flashlight:

## Do all optimizations you can
    
* Minimum SDK to Lollipop.
* Gradle build cache.
* Gradle Android Plugin DEX build cache.
* Mainframer :computer:

---

![bg original](images/max-holstered.jpg)

---

# Spek :game_die:

---

# Spek

```kotlin
@RunWith(JUnitPlatform::class)
class Spec : Spek({

    val system by memoized { System() }
    
    context("boot") {

        beforeEachTest {
            system.boot()
        }

        it("reports boot status") {
            assertThat(system.status()).isEqualTo(BOOTED)
        }
    }  
})
```

---

# Spek: versions

* `1.0.89`
   * `DescribeBody` → `Dsl` :bomb:
* `1.1.0-beta3`
   * `Dsl` → `SpecBody` :bomb:
* Docs mention `1.0`, `1.1.19`.
   * Do not exist.
* Moved from JUnit 4 to JUnit 5 at some point :bomb:

---

# Spek: versions :flashlight:

### :star: https://github.com/JetBrains/spek/issues/170

### *Please fix Spek versioning, compatibility and publishing*

---

# Spek: `memoized` caching

```kotlin
@RunWith(JUnitPlatform::class)
class OopsSpec : Spek({

    val system by memoized { System() }
    
    listOf(1, 2, 3).forEach { index ->

        context("boot") {
        
            it("boots") {}
        }
    }
}
```
Same `system` object for each run :gun:

---

# Spek: `memoized` caching

```kotlin
@RunWith(JUnitPlatform::class)
class OkSpec : Spek({

    val system by memoized { System() }
    
    listOf(1, 2, 3).forEach { index ->

        context("boot $index") {
        
            it("boots") {}
        }
    }
}
```
Unique `system` object for each run :balloon:

---

# Spek: `memoized` caching :flashlight:

### :star: https://github.com/JetBrains/spek/issues/210

### *Memoized and same test names*

---

# Spek: `memoized` before and after

```kotlin
@RunWith(JUnitPlatform::class)
class JustSpec : Spek({

    val system by memoized { System() }

    beforeEachTest {
        println("before $system")
    }

    afterEachTest {
        println("after $system")
    }
})
```
Before and after `system` are different.

---

# Spek: `memoized` before and after :flashlight:

### :star: https://github.com/JetBrains/spek/issues/213

### *Memoized value is not the same before and after test*

---

# Spek: JUnit

* Actually JUnit 5 from now on.
* Use `@RunWith(JUnitPlatform::class)` to work with JUnit 4.
* JUnit 5 is not released, so IJ may not work.
    * *OH HAI Android Studio 3 Beta.*
* `fon` and `fit` were removed, use Spek IJ plugin.
    * *Does not work with JUnit 4.*
* :sparkles: This is fun :sparkles:

---

# Spek

* Kind of advertized by JetBrains, but...
* Commits are two times a month on average :sleeping:
* Breaks compatibility easily :bomb:
* Docs feel outdated all the time.
* There are bugs. Remember vanilla JUnit stability?
* Also, have 100 K tests? Have to maintain it :rage:

---

![bg original](images/max-one-gun.jpg)

---

# Bonus round :video_game:

---

# Bonus round :video_game:

## You are on your own

### Prepare that YouTrack account...

---

# Bonus round :video_game:

> ...
> Kotlin is very reference semantics, itʼs a thin layer on top of Java, and so it perpetuates through a lot of the Javaisms in its model.
> ...
> If we had done an analog to that for Objective-C it would be like, everything is an NSObject and itʼs objc_msgSend everywhere, just with parentheses instead of square brackets.
> ...

*Chris Lattner, Swift* :bird:

---

# Bonus round :video_game:

* Java: Oracle, Red Hat, Intel, IBM, Eclipse, Twitter, JetBrains, etc.
* Kotlin: JetBrains.
* This is fine actually, but do not expect a miracle.
* For a single company a damn great language :heart:

---

# Conclusion

* There will be dragons :dragon:
* Kotlin will not make you a better developer.
    * It will give you tools though :hammer:
* Remember about Java underneath.
    * Swift can break things, Kotlin cannot :bird:
* The language is top-notch and well-designed.
* Be careful!

---

![bg original](images/max-two-guns.jpg)