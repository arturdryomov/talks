<!-- Tool: Marp 0.0.12 -->

<!-- $size: a4 -->

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

* :three: years
* Kotlin-only + RxJava from the start
* Kotlin EAP in production :fire:
  * `1.1-EAP` — 6 months
  * `1.2-EAP` — 3 months
* 99.9 % crash-free
* 2 week sprints
* :four: developers, :one: QA

---

# Juno Rider :blue_car:

* `cloc main/kotlin` — 56 K 
* `cloc test/kotlin` — 49 K
* `cloc androidTest/kotlin` — 17 K

---

# Style :lipstick:

---

# Checkstyle and Static Analysis

## So yeah, forget every tool for Java :sparkles:

---

# Specifications :triangular_ruler:

* Java AST (Abstract Syntax Tree)
    * Java-only
* PSI (Program Structure Interface)
    * IntelliJ IDEA-only
* UAST (Universal AST)
    * Java and Kotlin
    * Android Lint (since 3.1.0 — took a year to get there)

---

# Checkstyle

> How about not mapping it to AST and instead map it to UAST (Universal Abstract Syntax Tree)? That way also adding support for Kotlin, which is an official thing for Android now, can be supported.

> Checkstyle works only with AST as we depend on ANTLR and Java grammar.

https://github.com/checkstyle/checkstyle/issues/4369

---

# Checkstyle and Static Analysis :flashlight:

* Put your IntelliJ IDEA config into the repository.
* You can somewhat run IJ headless.
* Detekt + ktlint (work with PSI though).
* Android Lint is not actually tied to Android.
* Force warnings as errors.

  ```kotlin
  tasks.withType<KotlinCompile> {
      kotlinOptions {
          allWarningsAsErrors = true
      }
  }
  ```

---

# Infinity War

Let’s build something!

```kotlin
val gameBuilder = StringBuilder()

gameBuilder.append("tic")
gameBuilder.append("tac")
gameBuilder.append("toe")

val game = gameBuilder.toString()
```

---

# Infinity War

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

# Infinity War

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

# Infinity War

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

# Infinity War

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

# Infinity War

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

# Infinity War :imp:

* Better brew that coffee for long PRs :coffee:
* `CODE_STYLE.md`

---

# Weird Parts :jack_o_lantern:

---

# Destructuring

No joke, actually useful.

```kotlin
val (first, second) = pair
val (first, second, third) = triple

listOf(1 to "one", 2 to "two").map { (number, text) ->
    
    println("number is $number, text is $text")
}
```

---

# Destructuring

Supports `data class` and suggested by IDE.

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

# Destructuring

Component functions!

```kotlin
data class Person(
    lastName: String,
    firstName: String
) {
    fun component1() = firstName
    fun component2() = lastName
}
```

AWKWARD! Also — so easy to forget...

---

# Destructuring :flashlight:

## Just ban it unless `Pair` or `Triple`


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
* Make your own abstraction on top of a Java API.

---

# `null` safety!! :flashlight:

## Kotlin: The Problem with `null`

### https://arturdryomov.online/posts/kotlin-the-problem-with-null/

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
```
Use `?:` and `let` — embrace the language.

---

# Mockito

```kotlin
interface View {
    fun showMessage(message: String)
}

class Presenter {
    fun present(view: View) = view.showMessage(randomMessage())
}

@Test fun test() {
    val view = Mockito.mock<View>()
    presenter.present(view)
    
    verify(view).showMessage(Mockito.any()) // Crash!
}
```

---

# Mockito

```java
class Mockito {
    
    public static <T> T any() {
        return null;
    }
}
```

---

# Mockito

```kotlin
fun <T> anything(): T = null as T

@Test fun test() {
    val view = Mockito.mock<View>()
    presenter.present(view)
    
    verify(view).showMessage(anything()) // It works!
}
```

It works because of explicit nullable `T` declaration.

---

# Mockito :flashlight:

Aliases!

```kotlin
fun <T> eq(value: T): T = Mockito.eq<T>(obj)
fun <T> any(): T = Mockito.any<T>()

fun <T> whenever(call: T) = Mockito.`when`(call)
```

Yep, `when` is a part of the language syntax actually.

---

# Gson

```kotlin
data class Model {

    @SerializedName("model_field")
    val field: String

}

model.field.toString() // Crash!
```

---

# Gson

## Gson is happy to write `null` to properties

### Reflection is a thing in the Java world...

---

# Gson

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

`Validatable` is executed in _parse time_.

---

# Gson :flashlight:

Take a look at Moshi or Jackson

* Uses Kotlin native reflection :angel:
* Uses 5 MiB of application size.
  * OH HAI Android :sparkles:

---

# Optimizations :rocket:

---

# `inline` or `noinline`

* Neat, reduces methods count.
    * OH HAI Android :sparkles:
* Syntax feels like kernel development.
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
* Use annotation processing? +:one: build step.
* Just remember you are doing it :100: times per day.

---

# `kapt`

_Kotlin Annotation Processing Tool_

* Breaks incremental compilation.
* Throws errors.

  ```
  *_MemberInjector.java: error: package does not exist
  ```
---

# `kapt`

## `rm -f`

### https://arturdryomov.online/posts/a-dagger-to-remember/

---

# Assemble :clock9:

* Compilation is CPU intensive.
* Laptops have mobile CPUs.
* PCs are cheap, easy to upgrade...

---

# Mainframer :computer:

### https://github.com/gojuno/mainframer

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
    
* Gradle build cache.
* Mainframer :computer:

---

# Gradle Kotlin DSL :game_die:

---

# Kotlin versions

* Gradle 4.2 bundles Kotlin 1.1.4-3 for DSL
* Update project Kotlin 1.2-M1 → 1.2.0-beta-31...

```plain
Runtime files in the classpath should have the same version.
These files were found in the classpath:

.../dists/.../kotlin-stdlib-1.1.4-3.jar (version 1.1)
.../caches/.../kotlin-stdlib-1.2.0-beta-31.jar (version 1.2)
```

---

# Kotlin versions

## Sometimes versions are not compatible!

### https://github.com/gradle/kotlin-dsl/issues/519

---

# Groovy is in the air

```groovy
// Groovy
testOptions {
    unitTests.all {
        maxParallelForks = 2
    }
}
```
```kotlin
// Kotlin
testOptions {
    unitTests.all(KotlinClosure1<Test, Test>({
        apply {
            maxParallelForks = 2
        }
    }, this))
}
```

---

# Groovy is in the air

```java
import groovy.lang.Closure;

public class TestOptions {

    public void unitTests(Closure closure) {
        ConfigureUtil.configure(closure, unitTests);
    }
}
```

:star: https://issuetracker.google.com/issues/78547461

---

# Groovy is in the air

* Somebody, `N` years ago: _Non-Groovy Gradle DSL? WAT?_
* Somebody, 2017: _Let’s introduce non-Groovy DSL!_
  * `groovy.lang.Closure` → `org.gradle.api.Action`
  * Java-based DSL-agnostic Gradle API
* Plugin developers, 2017: _WAT?_

---

# Groovy is in the air :flashlight:

## Promote DSL-agnostic API

---

# Gradle Kotlin DSL

* IDE does not recognize files sometimes.
* Dynamic Groovy magic does not fit static Kotlin.
* Feels like code, works kind of like code.
* Hey, it’s alpha! :ghost:

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

# Versions

* `1.0.89`
   * `DescribeBody` → `Dsl` :bomb:
* `1.1.0-beta3`
   * `Dsl` → `SpecBody` :bomb:
* Docs mention `1.0`, `1.1.19`.
   * Do not exist.
* Moved from JUnit 4 to JUnit 5 at some point :bomb:

---

# Versions :flashlight:

### :star: https://github.com/JetBrains/spek/issues/170

### *Please fix Spek versioning, compatibility and publishing*

---

# `memoized` caching

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

# `memoized` caching

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

# `memoized` caching :flashlight:

### :star: https://github.com/JetBrains/spek/issues/210

### *Memoized and same test names*

---

# `memoized` before and after

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

# `memoized` before and after :flashlight:

### :star: https://github.com/JetBrains/spek/issues/213

### *Memoized value is not the same before and after test*

---

# JUnit

* Actually JUnit 5 from now on.
* Use `@RunWith(JUnitPlatform::class)` to work with JUnit 4.
* JUnit 5 is not released, so IJ may not work.
    * *OH HAI Android Studio 3*
* `fon` and `fit` were removed, use Spek IJ plugin.
    * *Does not work with JUnit 4.*
* :sparkles: This is fun :sparkles:

---

# Spek

* Kind of advertized by JetBrains, but...
  * Moved to Spek Framework recently.
* Breaks compatibility easily :bomb:
* Docs feel outdated all the time.
* There are bugs. Remember vanilla JUnit stability?
* Also, have 100 K tests? Have to maintain it :rage:

---

# Spek

## Take a look at Spectrum!

https://github.com/greghaskins/spectrum

Works well with Kotlin and Java.

---

# Bonus round :video_game:

---

# Bonus round :video_game:

## Less and less likely, but you are on your own

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

![bg original](images/the-fall.jpg)

---

# Conclusion

* Remember about Java underneath.
    * Swift can break things, Kotlin cannot :bird:
* Tooling can be... cruel.
* The language is top-notch and well-designed.
* Be careful!

---

![bg original](images/max-two-guns.jpg)