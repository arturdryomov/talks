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

---

# Juno Rider :blue_car:

* 3 years
* 100 % Kotlin
* Kotlin EAP in production :fire:
  * `1.1-EAP` — 6 months
  * `1.2-EAP` — 3 months
* 99.9 % crash-free
* 2 week sprints
* 4 developers, 1 QA

---

# Juno Rider :blue_car:

* `cloc main/kotlin` — 56 K 
* `cloc test/kotlin` — 49 K
* `cloc androidTest/kotlin` — 17 K

---

# Style :lipstick:

---

# Java Tools

## Nope :sparkles:

---

# Specifications :triangular_ruler:

* Java AST (Abstract Syntax Tree)
* PSI (Program Structure Interface)
* UAST (Universal AST)

---

# Checkstyle

> Checkstyle works only with AST as we depend on ANTLR and Java grammar.

https://github.com/checkstyle/checkstyle/issues/4369

---

# Kotlin Tools :flashlight:

* IntelliJ IDEA :heart:
* `detekt` + `ktlint`
* `android/lint` 
* `kotlinc --force`

  ```kotlin
  tasks.withType<KotlinCompile> {
      kotlinOptions {
          allWarningsAsErrors = true
      }
  }
  ```

---

# Infinity War :imp:


```kotlin
val gameBuilder = StringBuilder()

gameBuilder.append("tic")
gameBuilder.append("tac")
gameBuilder.append("toe")

val game = gameBuilder.toString()
```

---

# Infinity War :imp:


```kotlin
val game = StringBuilder().also {
    it.append("tic")
    it.append("tac")
    it.append("toe")
}
.toString()
```

---

# Infinity War :imp:

```kotlin
val game = StringBuilder().let {
    it.append("tic")
    it.append("tac")
    it.append("toe")

    it.toString()
}
```

---

# Infinity War :imp:

```kotlin
val game = StringBuilder().apply {
    append("tic")
    append("tac")
    append("toe")
}
.toString()
```

---

# Infinity War :imp:

```kotlin
val game = StringBuilder().run {
    append("tic")
    append("tac")
    append("toe")

    toString()
}
```

---

# Infinity War :imp:

```kotlin
val game = with(StringBuilder()) {
    append("tic")
    append("tac")
    append("toe")

    toString()
}
```

---

# Infinity War :flashlight:

* :coffee:?
* `CODE_STYLE.md`

---

# Weird Parts :jack_o_lantern:

---

# Destructuring


```kotlin
val (first, second) = Pair(1, 2)
val (first, second, third) = Triple(1, 2, 3)

listOf(1 to "one", 2 to "two").map { (number, text) ->
    
    println("number is $number, text is $text")
}
```

---

# Destructuring

```kotlin
data class Person(val firstName: String, val lastName: String)

val (firstName, lastName) = Person("f", "l")
```
:sparkles:
```kotlin
data class Person(val lastName: String, val firstName: String)

val (firstName, lastName) = Person("f", "l") // It works!
```

---

# Destructuring

```kotlin
data class Person(val firstName: String, val lastName: String)
{
    operator fun component1() = firstName
    operator fun component2() = lastName
}
```

---

# Destructuring :flashlight:

## :heart: `Pair`, `Triple`
## :broken_heart: `data class`


---

# `null` safety?

```kotlin
Observable.just(null)
```

:sparkles:

```java
public static <T> Observable<T> just(T item) {
    ObjectHelper.requireNonNull(item, "The item is null");
    ...
```

---

# `null` safety!!

## `@Nullable` + `@NonNull`

---

# `null` safety!! :flashlight:

* Docs.
* Annotations.
* Abstractions.

---

# RxJava 2

```kotlin
val o: Observable<Int?> = Observable.just(null)

// Runtime: NullPointerException
```

:sparkles:

```java
public static <T> Observable<T> just(T item) {
    ObjectHelper.requireNonNull(item, "The item is null");
    ...
```

---

# `Optional<T>`

* Swift: `?`, `nil` :arrow_right: `enum Optional`
* Kotlin: `?`, `null` :arrow_right: `null`

---

# Koptional :flashlight:

## https://github.com/gojuno/koptional

```kotlin
val some = Some(value)
val none = None

val optional = nullableValue.toOptional()
val nullable = o.toNullable()
```

---

# Mockito

```kotlin
interface Api {
    fun call(id: String)
}

class Service(private val api: Api) {
    fun call() = api.call(generateId())
}
```
```kotlin
@Test fun test() {
    val api = Mockito.mock<Api>()
    Service(api).call()
    
    verify(api).call(Mockito.any())
}

// Runtime: NullPointerException
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
fun <T> anything(): T = Mockito.any<T>()
```

```kotlin
@Test fun test() {
    val api = Mockito.mock<Api>()
    Service(api).call()
    
    verify(api).call(anything())
}
```

---

# Mockito :flashlight:

```kotlin
fun <T> eq(value: T): T = Mockito.eq<T>(obj)
fun <T> anything(): T = Mockito.any<T>()

fun <T> whenever(call: T) = Mockito.`when`(call)
```

---

# Gson

```kotlin
data class Model {

    @SerializedName("model_field")
    val field: String

}

val model = Gson().fromJson("{}", Model::javaClass)

// Runtime: OK

model.field.toString()

// Runtime: NullPointerException
```

---

# Gson

## Java Reflection

---

# Gson

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

# Gson :flashlight:

## Moshi vs. Jackson

### Kotlin Reflection :angel: (2.5 MiB)

---

# `null` safety!! :book:

## Kotlin: The Problem with `null`

### https://arturdryomov.online/posts/kotlin-the-problem-with-null/

---

# Optimizations :rocket:

---

# `inline`

```kotlin
private fun createId(): Int = 42
```
vs.
```kotlin
@Suppress("nothing_to_inline")
private inline fun createId(): Int = 42
```

---

# `inline`

### :star: https://youtrack.jetbrains.com/issue/KT-16769

### *Revisit compiler and IDE warning about inline functions*

---

# `const`

```kotlin
companion object {
    private val NUMBER = 42
}
```

:arrow_down:

```java
public static final class Companion {
    private final int NUMBER = 42;
    
    private final int getNUMBER() {
        return NUMBER;
    }
}
```

---

# `const`

```kotlin
companion object {
    private const val NUMBER = 42
}
```
:arrow_down:

```java
public static final class Companion {
    private static final int NUMBER = 42;
}
```

---

# `const`

* Primitives + `String`
* `kotlinc`?

---


## Tools :arrow_right: Kotlin :arrow_right: Show Kotlin Bytecode

---

# Tooling :hammer:

---

# Bugs :bug:

```kotlin
fun multiply(double: Double) = double * double
```

:arrow_down: 1.1.3

```text
org.jetbrains.kotlin.kapt3.diagnostic.KaptError
Error while annotation processing
```

---

# Bugs :bug:

### :star: https://youtrack.jetbrains.com/issue/KT-18377

### *Syntax error while generating kapt stubs*

---

# Bugs :bug:

## Are a thing!

---

# Assemble :clock9:

* `javac`
* :heavy_plus_sign: `kotlinc`
* :heavy_plus_sign: `kapt`
* :heavy_multiplication_x: :100:

---

# `kapt`

_Kotlin Annotation Processing Tool_

* Breaks incremental compilation.
* Throws errors.

  ```
  *_MemberInjector.java: error: package does not exist
  ```
---

# `kapt` :book:

## A Dagger to Remember

### https://arturdryomov.online/posts/a-dagger-to-remember/

---

# Assemble :clock9:

* :computer: — `i5-5257U @ 2.70GHz`
* :tv: — `i7-6700K @ 4.00GHz`

---

# Mainframer

## https://github.com/gojuno/mainframer

```plain
$ rsync laptop mainframe
$ make
$ rsync mainframe laptop
```

---

# Mainframer

```plain
$ ./gradlew assembleDebug
```

:thumbsdown: 4 minutes 10 seconds :fire:

```plain
$ mainframer ./gradlew assembleDebug
```

:thumbsup: 1 minute 10 seconds

---

# Assemble :clock9: :flashlight:
    
* Remove everything possible.
* Gradle build cache.
* Mainframer.

---

# Gradle Kotlin DSL :game_die:

---

# Kotlin versions

* Gradle 4.2 — 1.1.4-3
* Project — 1.2.0-beta-31

```plain
Runtime files in the classpath should have the same version.
These files were found in the classpath:

.../dists/.../kotlin-stdlib-1.1.4-3.jar (version 1.1)
.../caches/.../kotlin-stdlib-1.2.0-beta-31.jar (version 1.2)
```

---

# Kotlin versions :flashlight:

### https://github.com/gradle/kotlin-dsl/issues/519

---

# Groovy

```groovy
unitTests.all {
    maxParallelForks = 2
}
```

:arrow_down:

```kotlin
unitTests.all(KotlinClosure1<Test, Test>({
    apply {
        maxParallelForks = 2
    }
}, this))
```

---

# Groovy

```java
import groovy.lang.Closure;

public void unitTests(Closure closure)
```

:star: https://issuetracker.google.com/issues/78547461

---

# Groovy

* 2012: `groovy.lang.Closure`
* 2017: `org.gradle.api.Action`

---

# Groovy :flashlight:

## Promote DSL-agnostic API

---

# Gradle Kotlin DSL

* IDE :fire:
* Dynamic Groovy :fire:
* Workflow :fire:
* Alpha! :ghost:

---

# Spek :game_die:

---

# JUnit

```kotlin
class SystemTest {

    lateinit var system: System
    
    @BeforeEach fun create() {
        system = System()
    }
    
    @BeforeEach fun start() {
        system.boot()
    }
    
    @Test fun `has boot status`() {
        assertThat(system.status()).isEqualTo(BOOTED)
    }
    
    @Test fun `has positive uptime`() {
    	assertThat(system.uptime()).isNotZero()
    }
}
```

---

# Spek

```kotlin
class SystemSpec : Spek({

    val system by memoized { System() }
    
    context("boot") {

        beforeEachTest {
            system.boot()
        }

        it("has boot status") {
            assertThat(system.status()).isEqualTo(BOOTED)
        }
        
        it("has positive uptime") {
            assertThat(system.uptime()).isNotZero()
        }
    }  
})
```

---

# Versions

* `1.0.89`: `DescribeBody` :arrow_right: `Dsl` :bomb:
* `1.1.0-beta3`: `Dsl` :arrow_right: `SpecBody` :bomb:
* `1.0`, `1.1.19`: do not exist.
* JUnit 4 :arrow_right: 5 :bomb:

---

# `memoized` before and after

```kotlin
class JustSpec : Spek({

    val system by memoized { System() }

    beforeEachTest {
        println("before $system") // system_before
    }

    afterEachTest {
        println("after $system") // system_after
    }
})

// system_before != system_after
```

---

# `memoized` before and after :flashlight:

### :star: https://github.com/JetBrains/spek/issues/213

### *Memoized value is not the same before and after test*

---

# JUnit

* JUnit 5!
  * Android Studio :broken_heart:
* JUnit 4?
  * `@RunWith(JUnitPlatform::class)`
* `fon`, `fit` :arrow_right: Spek IJ plugin.
    * JUnit 4 :broken_heart:
* :sparkles: Fun :sparkles:

---

# Spek

* `jetbrains/spek` :arrow_right: `spekframework/spek`
* Compatibility :bomb:
* Docs :bomb:
* Bugs :bomb:
* 100 K tests :rage:
* `2.0` :angel:

---

# Spek :flashlight:

## Spectrum, KotlinTest

---

# Conclusion :8ball:

---


# https://youtrack.jetbrains.com/issues/KT

---

> ...
> Kotlin is a thin layer on top of Java, and so it perpetuates through a lot of the Javaisms in its model.
> ...

*Chris Lattner (Swift, LLVM, Clang)* :bird:

---

# :tophat:

* Java — Executive Committee
  * Oracle, Red Hat, Intel, IBM, Eclipse, Twitter, JetBrains...
  * 24 members
* Kotlin — Language Committee
  * JetBrains, Google
  * 3 members

---

![bg original](images/the-fall.jpg)

---

# Conclusion :8ball:

* JVM :ghost:
* Tooling :ghost:
* Language :heart:

---

![bg original](images/max-two-guns.jpg)