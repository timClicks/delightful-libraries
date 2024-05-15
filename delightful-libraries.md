# Writing delightful software libraries in Rust

- **Author**: Tim McNamara, @timClicks. <tim@mcnamara.nz>
- **Licence**: CC-BY-SA 4.0
- **Version**: 0.0.0
- **Status**: VERY INCOMPLETE

:::info
This document has been created as part of my Rust Fellowship for the June 2023-2024 year. Thank you to all of the sponsors who have provided financial support for the program and to the foundation for facilitating it. 
:::

# Introduction

If you're writing a library, then you are writing software to be helpful. You're absorbing the cost of writing code and relieving that burden from others. Thank you.

You should expect that people see your software as a risk. Every dependency adds some weight to their project. Therefore, an important responsibility is to provide a good first impression. Although this seems superficial, to create a good first impression, everything needs to be in place.

Most developers that use your crate will be novice Rust programmers. This is likely to remain so for many years. Rust is growing very rapidly. This means that there will always be a large cohort of users who will get stuck.

Every new tool imposes a productivity burden on its adopters. We look to new things to speed things up. However, when we're learning, we slow down.

Most developers are busy and distracted. They don't want to have to think too much. Your code should fit with patterns that they already understand. Your documentation should explain how.

Most developers are opinionated and fussy. Computer programming is not an easy skill to acquire. To become proficient, programmers need to learn how to push through adversity and muddle their way through.

Avoid magic. It's rarely what people want to see. Once people understand traits, most magic comes from macros.

Use cargo. Some businesses have their own build systems. It's generally a better idea to teach the build system about cargo, rather than teaching it to compile Rust directly. The cargo ecosystem is very rich and it contains many plugins. Avoiding them has a large cost.

It's true that Cargo enforces semantic versioning. However it's also true that, Cargo imposes additional constraints upon Rust projects than what the semantic versioning spec actually requires. Strict compliance with semantic versioning means that there is no API stability when the library has not reached 1.0. However, the Rust community expects that pre-1.0 libraries to treat their minor version number as a major version number. When a breaking change is introduced in a patch version, [people complain](https://twitter.com/5422m4n/status/1785301639739412492) .

Another aspect of Rust's semantic versioning culture is that it's very hard for projects to reach 1.0. Once they're there, it's very difficult to reach the next major version.

Another quirk about how semantic versioning is adopted in practice is that a major version change *implies* breaking changes. The spec is a little more flexible.

Generally speaking, it's better to spend too long on pre-1.0 rather than impose breaking changes.

If you have a popular library, making breaking changes imposes significant costs on your user base. You'll need to invest political capital for you make this change.  Be conscious with your design decisions.

# Rust concepts to have good understanding of

- trait system as basis, e.g. Sized
- memory model & ownership: registers/stack/heap/static
- Result, Option, std::error::Error
- smart pointers
- interior mutability; Cell vs RefCell
- mutable global state (incl. in an async context)
- how async actually works, e.g. `Future` trait


# Types of libraries

## data structures

## client libraries


## Foundational ecosystem crates

Some library crates exist primarily as a way to expose traits to the world. Often this produces an ecosystem of libraries that work well together.

- The [log crate] doesn't actually provide logging. It provides facilities for other crates to do logging. RUST_LOG
- The [rand crate] provides central traits, including`Rng` and `SeedableRng`. It also re-exports several implementations of those traits provided by additional crates behind feature flags.
- The geospatial community maintains the [geo crate], which can describe what geometries such as what a "point", "line" or a "polygon" mean.

[rand crate]: https://crates.io/crates/rand
[log crate]: https://crates.io/crates/log
[geo crate]: https://crates.io/crates/geo

This pattern occurs largely due to the orphan rule and partially due to cultural norms.

<!--
Occasionally, this becomes an issue. 

Because of its centrality within the ecosystem, futures/0.3 is now the de facto futures/1.0.

 Serialization has generally become synonymous with the serde crate's `Serialize` and `Deserialize` traits. Serde is a relatively expensive dependency to bring in though, as it uses lots of compile-time macros to enable serialization of all of Rust's primitive types. When your crate doesn't need all of that functionality, you still pay for all of it. Arguably, a more neutral set of traits could be created that exists outside of any given serialization system. Serde's traits are not a good fit for this model, because they're heavily tied to the visitor pattern. 
 -->

# Versioning

The Rust community practices semantic versioning, enforced through its build tool cargo and strong community norms.

# Documentation

The project's documentation is how it makes its first impression. Sending people to the `examples` directory is not enough. Yes, developers can look through the code, and they will if they're already invested in the project, but they won't want to wade through your source code to get a dist of what's happening.

Distinguish between 

The majority of people reading your documentation will not be native English speakers. This does not mean that they're unable to speak the language, but it does mean that you should be wary if you use uncommon phrases and idioms. 

The general advice about written communication also applies to technical documentation. Language is surprisingly difficult to do at scale. There is a lot of context missing when we write.

When writing, consider the audience's needs. A common problem is expert blindness. We forget what things look like as a beginner. Because we know the tools so well, we don't need to consciously think about which steps we need to take, therefore it's hard to explicitly state every step.

Ask others to read through your tutorial. If you're feeling brave, you can also do this over a video call to get immediate feedback about things that are confusing.

## Design and Architecture

You should generally use dedicated site, perhaps generated with [mdBook], for describing design documentation.

[mdBook]: https://github.com/rust-lang/mdBook

## Reference

I dislike silly or cute examples because use they often rely on cultural idioms. Most of your readers won't share that cultural context. Therefore, it's unlikely that they'll have the intended effect. However, there's a strong argument to me made that you should feel empowered to do whatever you want. As long as you're deliberate, and the tone of the examples matches the tone of the rest of the project, then you can have fun.

Try to avoid overused tropes.

Animal, Cat and Dog. Employee, Department, Customer. Products.

Foo, Bar, and Baz as variable names.

## Tutorial

A tutorial introduction is often appreciated. This will guide people through the process of what setup needs to occur and give them a good feel for the important steps that they'll need to follow to be productive.

Dedicated websites generated with [mdBook] are common. mdBook is a command-line tool that is essentially a static-site generator, with special support for Rust "code fences". mdBook can test all of the examples to ensure that they work.

```markdown
# whatevs

We are documenting our library.

,,,rust
use lib;

fn main() {

} 
,,,

This will be printed as HTML.

```

The `clap` project bundles its tutorial into the main repository, hidden behind a feature flag. This ensures that the tutorial is available on docs.rs. 

If your project involves introducing a paradigm, you may also



Examples: `regex`, `clap`, and a number of parsing libraries such as [`chumsky`], [lalrprop]

[mdBook]: https://rust-lang.github.io/mdBook/
[`regex`]: https://github.com/rust-lang/regex
[`chumsky`]:  https://github.com/zesterer/chumsky/blob/main/tutorial.md

Writing effective tutorials is very difficult. To start with, you're likely to underestimate the task at hand because they're superficially easy. Tutorials "just" describe how to use the tool while you're starting.

Tutorials are challenging to keep up-to-date. It's rare to see software teams rigorously check that their tutorials are still current.

Perhaps add an item in your release process to check the tutorial when releasing minor versions?

## software as poetry

Software projects are creative endeavour. It is subject to many of the same social pressures as other things that people produce, including fashion and trends.

Software products are deceptively expensive. The last 5% takes up 50% of the effort.



## logging / output

Generally speaking, libraries should be quiet. 

LOG

### Error types

When you're starting out, you should generally take some time to understand the fundamentals.

We need to distinguish between two senses of the word "error". The first are types that implement `std::error::Error` trait. The second are types that are returned as `E` within `Err(E)`. While there's a very significant overlap, they're actually not equivalent, as there's no trait bound on `E` within `Err`.

Here is the [definition of `std::result::Result`](https://doc.rust-lang.org/src/core/result.rs.html#495), in case you were curious:

```rust
#[must_use]
enum Result<T, E> {
  Ok(T),
  Err(E)
}
```

`Result` follows the convention of adding trait bounds as late as possible, in methods that need them. They're provided on a few methods, but generally speaking the intent is to place as few restrictions as possible.

Arguably, there is also a third sense that's useful to be aware of. Sometimes the word error is used for anything that happens when something goes wrong. For example, people could think of the error message printed during panic is an error. 

The `std::error::Error` trait is concerned with reporting. Its role is to ensure that something can be printed or logged when an error occurs. That's why any type that implements `Display` can implement `Error`.

`Err(E)` is concerned with control flow. When `Err(E)` is returned from a function, the caller has the responsibility to do something with it. Notice that the definition in the snippet above contains the annotation `#[must_use]`.

There are two common strategies for errors, and two others that I wish to point out.  The common approaches relate to the `Error` trait and the third makes use of the generality of `Err(E)`. The fourth optimizes for size.

When writing library code, you'll often create an enum that has a variant for each kind of problem that you think that you might expect. This will often include wrapping upstream error types, then implementing `From<U> for E` where `U` is the upstream type for your error `E`.

This code looks a lot like this:

```rust
enum E

```



error type that implements the `Error` trait write 

A pattern:

```rust
use std::fmt::Display;
use std::error::Error as ErrorTrait;
use std::io;

struct Error {
    kind: ErrorKind,
}

enum ErrorKind {
	Missing,
	Io(io::Error),
}

impl From<io::Error> for Error {
	
}
```

This can be greatly simplified. Use [`thiserror`] to a) enable your own "error codes" and b) to wrap upstream errors.

Error types should try to stay small. They're used in many code paths and can be surprisingly bulky.


[`thiserror`]: https://crates.io/crates/thiserror

### Diagnostics

If you need to provide users with error messages relating to the input that they're providing, then you should consider pulling in a library that can annotate text with the location of errors as well as providing hints. 

There is a small family tree of crates that provide attractive reports in the console.

 - [`miette`]: the diagnostics library de jour. Very capable, but quite complex. Has good support for working as a library.
- [`ariadne`]: very pretty, but an increase up the complexity curve from `codespan`
- [`codespan`]: original derivative of the diagnostics provided by `rustc`

[`ariadne`]: https://lib.rs/crates/ariadne
[`codespan`]: https://github.com/brendanzab/codespan
[`miette`]: https://docs.rs/ariadne

## working code

This is often one of the things that we assume, but it's worth stating explicitly. Your code should do what it says that it does. 

Done is better than perfect. But if you want people to use it, then it needs to be done well.




## development policies

Make an intentional choice about which development practices to follow.

```
#[deny(unsafe)]
```

## build settings

Useful references

https://github.com/rust-lang/regex/blob/master/Cargo.toml
https://github.com/rayon-rs/rayon/blob/main/Cargo.toml

### Docs.rs settings

```toml
[package.metadata.docs.rs]
all-features = true
```

### Feature flags

Use feature flags liberally, but hold off adding them until your library has become fairly stable. Otherwise they'll become another distraction for your team.

You do need to document what each feature does, however. 

It may be better to create multiple crates. 

### rustc codegen options

As a general point, if you're digging into these sorts of options, then you should have significant knowledge about your compile targets. The defaults should generally be "good enough".

https://doc.rust-lang.org/rustc/codegen-options/index.html#panic

Set `+crt-static` to enable forwards-compatibility. It's likely that the Rust project will adjust its defaults to use dynamic linking under Musl.

### Release profile
Cargo supports the notion of a "profile". Each profile has a different configuration settings.

In general, the release profile takes the most time to compile, but programs that are built with it run the fastest.

Recommended settings.

```toml
# dev - intended for development
[profile.dev]
opt-level = 2

# release - intended for public distribution
# 
# This profile produces a very fast executable, at the cost of
# significantly slower compilation speed and increased memory
# requirements.
# 
# Note: to enable cross-language link-time optimization (LTO),
#       the linker plugin needs to be provided via RUSTFLAGS
[profile.release]
codegen-units = 1
lto = true
strip = true
panic = "abort"

# release-internal - intended for internal distribution
# 
# This profile is intended for user-acceptance testing, 
# auditing, and other internal uses. 
[profile.release-internal]
codegen-units = 16
lto = "thin"
strip = false
debug = true
incremental = true
```

Some comments.

- `codegen-units = 1` is very expensive. It reduces removes parallelism.
- `panic = "abort"` may be too abrupt. However, it's intended purpose is error recovery, and users of your library are unlikely to be able to recover from errors that occur in your code.
- `debug` symbols are very useful when you need them.

<!--
@predrag

> panic = "abort"
 
I would heavily qualify this recommendation. It may break some libraries, and it probably isn't a good fit for beginners who might not be in a good position to diagnose and debug that breakage.

-->


#### Consider an additional profile to distinguish public or internal distribution

If your company has a quality assurance step before any public builds are made, or wishes to create, consider creating a Cargo profile for that purpose. The cargo-dist project recommends using the name `dist` for this purpose.

An alternative strategy is to create a profile for *internal* releases. I recommend this approach as it maintains the original intent of the release profile. Using what people already know means that it's less likely that the incorrect build will be shipped.

#### Link-time optimization (LTO)

LTO is generally a good thing. It is likely to produce builds that run more efficiently. However, it does impose a significant compile-time cost. You'll need a computer with sufficient memory to maintain all of the symbol tables at compile-time.

```
lto = 1
```

Cargo cannot provide cross-language LTO, that is, optimization over the FFI boundary, by itself. For this, you'll need to provide a compiler plugin to LLVM via environment variables.

Cargo profile. Consider LTO. For gather data, you need profiling and benchmarks.

# Techniques for stainless Rust

## Information hiding

Using a `pub` field means that you've exposed your internal representation and have committed to maintaining its presence until the next major version.

## Traits and preludes

One of the biggest issues that you'll face as a library author, especially with beginning Rust programmers, is that they'll forget to import a trait. Then methods won't be available on types and the error messages will be cryptic. Users will copy + paste from the HTML documentation, but the examples in the docs hide the imports.

There are a few ways around this. One is to use fewer traits. Another is to use a prelude, so that it's harder to forget traits. A third, less obvious, method is to ensure that the `use` statement is at the top of the code examples. `rustup`

## Feature gates

Feature flags can be used by your users to decrease the compile times, because they only pay the cost of what they use. Use modules as the barrier to keep your internal code complexity manageable. 

A good example of this approach is the `tokio` crate, which provides many features of almost every aspect. It also includes a `full` feature, which is especially useful for when people are starting out and/or following a tutorial.

Most features should be enabled by default. When users are starting out, they don't have enough information to understand which features to opt into.  It's better to give them the full ensemble, then provide them with the ability to whittle that down.

## Tip: use dependencies

It's somewhat of a meme that the Rust community uses too many libraries. However, be wary of suggestions that you have "too many dependencies" as a path to speed up compilation times.

If you copy + paste code from other places, you guarantee that it'll be recompiled. Using it from a (small) crate increases the likelihood that your users will be able to benefit from caching. Moreover, crates are compiled independently and concurrently.

One argument against using 3rd party dependencies though is that you'll expose your team to the risk of supply chain attacks.

However, individual crates are easier to audit that individual functions, or smaller sections of code. If you've selected sections of another implementation to include into your system

## Tip: measure compilation times

When building your code, add `--timings`. This invokes [cargo-timings](https://doc.rust-lang.org/nightly/cargo/reference/timings.html), which produces an easy-to-read HTML report in your project's `targets/cargo-timings` directory that will help to reveal bottlenecks. 

```shell
$ cargo clean && cargo build --release --timings
```

If your build infrastructure supports it, add "time for clean build" to your metrics.

## Increasing build speed

There are two ways to make computers go faster. The best way is to do less work. The alternative is to buy faster hardware.

### Split large crates into pieces

Smaller crates build faster and experience less churn during development. A crate is the "unit of compilation" within the Rust ecosystem, meaning that crates can be built independently in parallel. When you're making code changes, you typically are making changes to one place. Therefore, Rust won't need to recompile the whole project.

### Use a single version of each dependency

Check whether you're building multiple versions of a library in your dependency graph.
 
 Where possible, upgrade the older versions so that only a single version is needed.
 
Upgrading upstream might require sending a PR upstream for them to upgrade their version in their code. If they're slow at approving it, you can 
[override upstream ](https://doc.rust-lang.org/nightly/cargo/reference/overriding-dependencies.html#the-patch-section) by using the `[patch]` functionality within Cargo to replace a version of upstream with a version that you control. The ["Testing a bugfix" section](https://doc.rust-lang.org/nightly/cargo/reference/overriding-dependencies.html#testing-a-bugfix) of the Overriding Dependencies chapter of the Cargo Book details this process.

Sometimes, you may pin an old version of a dependency to support compilation with an older version of Rust.

Remember that you can provide dependencies at the workspace level to ensure that your workspace members all use the same version without needing to do additional work.

### Add feature flags to your library

Feature flags are a good way to facilitate speeding up builds. 
### Limit features from your dependencies

When your library is nearing stability, it can be a good idea to start pruning the features that you need from your dependencies. As an example, `tokio` and `regex` both come with many features that are not necessary for all projects.

The regex crate is a good example of a crate that's quite demanding on the CPU

## Technique: hiding methods that need to be public for testing

One of the unfortunate aspects of Rust's testing regime is that it can require you to expose your internal API.

You have a few strategies to to mitigate this.

The easiest is to annotate your function with `#[doc(hidden)]`. This'll make it hard to find.

Something that's available to you, but is unlikely to be worthwhile, is to mark the function as deprecated. That will generate a loud warning whenever someone compiles their code with your internal-yet-unfortunately-public API. Sadly, the compiler will also nag you when you run your test suite. Most importantly though,  `cargo-semver-checks` will consider this part of your public API, even though it's marked as `#[doc(hidden)]`, and you'll break semantic versioning. 

A related option is to mark the function as `unsafe`. This generates a similar anxiety-laden 

```rust
#[doc(hidden)]
pub unsafe fn _public_for_testing_only() {
    // ...
}
```


## Technique: making it impossible to call an internal method, aka sealing a trait

Here's some code seen in the wild:
 
```rust
/// DO NOT CALL THIS.
///
/// Clears the cache - used for benchmarking and testing purposes 
/// to clear the cache. Calling this will invalidate any previously
/// created `UStr`s and probably cause your house to burn down. 
/// DO NOT CALL THIS.
///
/// # Safety
/// DO NOT CALL THIS.
#[doc(hidden)]
pub unsafe fn _clear_cache() {
    for m in STRING_CACHE.0.iter() {
        m.lock().clear();
    }
}
```
ref: https://github.com/anderslanglands/ustr/blob/10a780a01196df9fa9d240bb0dcb14aae364a416/src/lib.rs#L416C22-L429C22

You should read the [Definitive Guide to Sealed Traits in Rust](https://predr.ag/blog/definitive-guide-to-sealed-traits-in-rust/).

## Technique: compensate for the lack of atomic types in the target platform

If you require atomic operations, but you are compiling for a target without them, then you need to emulate them within software. As seen by the [log crate](https://github.com/rust-lang/log/blob/c879b011a8ac662545adf9484d9a668ebcf9b814/src/lib.rs#L404), you can make use of  `std::cell:Cell`.

One thing that you may wish to confirm before copying the code below is whether you should implement `Sync` for the type. The in-line comment does so, but with am assumption: "Any platform without atomics is unlikely to have multiple cores, so writing via Cell will not be a race condition."

Code:

```rust
#[cfg(target_has_atomic = "ptr")]
use std::sync::atomic::{AtomicUsize, Ordering};

#[cfg(not(target_has_atomic = "ptr"))]
use std::cell::Cell;
#[cfg(not(target_has_atomic = "ptr"))]
use std::sync::atomic::Ordering;

#[cfg(not(target_has_atomic = "ptr"))]
struct AtomicUsize {
    v: Cell<usize>,
}

#[cfg(not(target_has_atomic = "ptr"))]
impl AtomicUsize {
    const fn new(v: usize) -> AtomicUsize {
        AtomicUsize { v: Cell::new(v) }
    }

    fn load(&self, _order: Ordering) -> usize {
        self.v.get()
    }

    fn store(&self, val: usize, _order: Ordering) {
        self.v.set(val)
    }

    #[cfg(target_has_atomic = "ptr")]
    fn compare_exchange(
        &self,
        current: usize,
        new: usize,
        _success: Ordering,
        _failure: Ordering,
    ) -> Result<usize, usize> {
        let prev = self.v.get();
        if current == prev {
            self.v.set(new);
        }
        Ok(prev)
    }
}

// Any platform without atomics is unlikely to have multiple cores, so
// writing via Cell will not be a race condition.
#[cfg(not(target_has_atomic = "ptr"))]
unsafe impl Sync for AtomicUsize {}
```

## Supporting older compilers

You may find yourself wanting to support older compilers, but make use of features from new compilers when they're available. The `rustversion` crate provides this, through conditional compilation.

As an example, it provides the ability to mark functions as `const` where this is supported by the compiler.

```rust
use std::time::Duration;

#[rustversion::attr(since(1.32), const)]
pub fn duration_as_days(dur: Duration) -> u64 {
    dur.as_secs() / 60 / 60 / 24
}
```

With macros expanded, you can see that the function's definition has been rewritten:

```rust
$ cargo +nightly rustc --profile=check --lib -- -Zunpretty=expanded
// (prelude omitted)
use std::time::Duration;

pub const fn duration_as_days(dur: Duration) -> u64 {
    dur.as_secs() / 60 / 60 / 24
}
```


# Projects

## rand_xorshift

Reference implementation: https://crates.io/crates/rand_xorshift


## Arc\<T> vs Box\<T>

## Project: string interning library

References

- https://github.com/anderslanglands/ustr


# Reporting error messages

One of the most compelling things about Rust is its error messages

# managing unsafe

Project

- Run [ustr](https://github.com/anderslanglands/ustr) under Miri




# code style

The advice in this section is advice provided by an opinonated software developer. You're very welcome to disagree and select your own principles.

## Be consistent with the standard library

A strong guiding principle is that you should seek to follow established patterns, rather than invent your own. Following established norms decreases cognitive load, increases your users' productivity, and increases retention. You're far less likely to get people implementing their own libraries if yours works well.

Result and Error types. If you are implementing your own error type, follow the pattern provided by the standard library and create a type alias over `std::result::Result<T, E>` with your specialized form of `E`. You're not required to implement the `std::error::Error` trait -- and in the cases when you're inspecting user input, you probably don't want to. Instead you can pass the original data back to the caller -- but it often helps.

Understand the different roles between the `std::error::Error` trait and the `Result::Err` enum variant. It's 

### Avoid `ctx` params

In some language communities, it's fairly common to see a context argument created via some init (initialization) method, then passed around by reference when it's needed. 

```go
ctx := init_context()
build(&ctx)
```

In Rust, you would generally be better off creating a concrete type that contains the context, then creating methods from that, making use of the implicit `self` parameter.


### Avoid name stuttering

Rust's standard library has a tendency of  This incud

### Avoid "params structs"



### Use standard formatting

Use `rustfmt`.


### release hygiene

- check for outdated dependencies (`cargo outdated -d 1`)
- check for security issues (`cargo audit`)
- check against all features and all targets (`cargo hack check --feature-powerset --no-dev-deps`)



## Continuous integration

semantic versioning breakages - `cargo semver-checks`. 

use automation where possible, e.g. `cargo release` or `release-plz`, and checklists where that's impossible.

Make your code auditable. The hash of git tag of your release should match the hash of what's uploaded to crates.io. This implies avoiding `cargo release --allow-dirty`. To provide additional assurance, you should also ensure that your release artifacts are built using a public toolchain, e.g. on GitHub Actions or similar. Once support for [Github's Attestations](https://github.blog/2024-05-02-introducing-artifact-attestations-now-in-public-beta/) becomes generally available, you should move to this also. 

If your code accepts input from the outside world, you should include fuzz testing.

## reducing the number of dependencies

Consider specialized dependencies. For example, although `serde` is by far the most common serialization crate, it's by no means the only option. It's quite bulky and is somewhat expensive to compile. You could consider `rkyv` (pronounced archive), for example.

Where possible, move dependencies to `[build-dependencies]` and `[dev-depdencies]`.

Try to reduce the number of features that you're including from upstream. For your dependencies, is it possible to build with a subset of the crate, e.g. `cargo --no-default-features --features <feature>...` ? If so, then do it! Pruning as much away from build time will increase the speed for everyone.

At least, being prudent with features means that it's less likely that you'll end up negatively impacting others' build times. Cargo will take the set of all features. So if one of your dependencies' dependencies still uses a feature that you've opted out of, your build will still include it.

# For projects being written in large enterprises

If you're a professional developer within a large enterprise, the expectations are higher. 

You also have many more resources though, so hopefully things even out. One of the advantages you will have is access to more compute resources. Ensure that your testing setup is robust.

Consider advanced approaches, such as property-based testing (`quickcheck` and `proptest`), model checking (Kani) fuzzing, [snapshot testing] and [mutation testing].

[`proptest`]: https://github.com/proptest-rs/proptest
[mutation testing]: https://mutants.rs/
[snapshot testing]: https://docs.rs/insta/

## Leadership buy-in is required

As your library grows, you'll need to support it. This will require dedicated time. Someone will need to fund that .

1.0 will be perceived as an indicator of quality assurance.

If your company has a heavy metrics culture, being an open source maintainer is difficult sell in the promo pack. 

## project management

Lots of the difficulty in open source relates to the mechanics of getting people to work together. Coordination is very difficult. Open source spans many boundaries, such as organisational and cultural.  

Developing an open source library means exposing some non-zero proportion of your company's communication to public view.

## create a public face

[todo: think through]

Decide if there should be a person who represents the project.

## define the level of community

Open source projects suffer because there's usually a disconnect between the status of different audiences. 

Most large open source packages that you use are maintained by someone at work. They're commercially backed, rather than community focused. 

External contributors are at a disadvantage.

This isn't something that you can avoid. But it is possible to mitigate its worst impacts. Most of the prooblems occur because of the differences between what people expect and what happens in practice. Be explicit with your intended approach to the community.
## splitting code into their own crates 

Large projects should be split into smaller crates within a shared workspace. This will speed up build times for your team.

Avoid creating any API guarantees though. Internal crates should crates have `version = "0.0.0"` in their Cargo manifest.

If you're creating code for multiple platforms, it can be useful to use sub-modules to split apart platform-specific code. This is how the standard library deals with its operating system-specific file APIs.

## be honest

Be honest with (potential) users of your library, particularly around the level of time that you have available for new feature development vs bug fixes. 

You don't want to alienate people. The reason why you created the library in the first place was to make someone else's life easier. Creating more stress is the opposite of that.

## define done

It can be helpful to describe what finished means. That allows you to say things like, "we're not done yet".

Creating a clear goal also allows you to say what's not in scope.

## legal

Be intentional with your choice of licence. While it's arguably unethical for large enterprises to freeload off of your work, it's not illegal for them to comply with the licence that you chose for your project. 

The Rust community tends to dual-licence code under MIT/Apache-2. This dual-licencing approach is inherited from the Mozilla-era, whereby the Firefox codebase was tri-licenced.

This community default allows for maximum reuse, however this isn't always what creators want. Consider how you will feel if an entity that you strongly dislike makes use of your software.

This is becoming and increasingly fraught topic when discussing the sustainability of open source and the pattern of creating a company based on open source package, which is quickly relicenced when its shareholders need to profit. 

Some alternatives to consider:

Consider the licencing of your documentation. It's conventional, but not compulsory, to use the same licence for docs and code. Moreover, software licences are generally poor fits for prose, and vice-versa. 

Perhaps the small code examples in your documentation should be licensed under a much more liberal licence, say BSD-0.

### Make sure that your library's metadata is machine-readable

While it can feel tiresome, it's a very good idea to sure that your project's metadata in its manifest (Cargo.toml) is complete and accurate.

SPDX identifiers.

### Limit exporting others' crates 

If you're working in the networked services domain space, one of the biggest decisions that you'll need to make is whether to attempt to stay neutral to async runtimes or to fold into Tokio. 

Other libraries have a similar decision relating to serialization. It's somewhat easy say that [`serde`] is the dominant player in that area, so it's a sensible choice to rely on it. However, serde is a general-purpose serialization system that provides flexibility at the expense of performance. It's conceivable that it may be replaced over time.

Both of these cases are examples of a broader issue. Should you tie your project to your dependencies? That is, if you expose a dependency's traits/types in your public API, then you're stuck with them until your next major release. This can be a concern if you don't have control.

Pragmatism can be instructive here. AWS decided to require Tokio and serde when it released version 1.0 of its Rust SDK.

Many crates provide a `serde` feature flag.

```rust
#[cfg(feature="serde1")] use serde::{Serialize, Deserialize};

#[derive(Clone, PartialEq, Eq)]
#[cfg_attr(feature="serde1", derive(Serialize,Deserialize))]
pub struct Greeting {
    name: String,
}
```

[`serde`]: htps://serde.rs

## Minimum stable Rust version (MSRV)

Avoid advertising a MSRV until your project establishes some maturity.

When considering MSRV, consider your target platforms. You may wish to ensure that your project will build natively on your intended platform. Maintaining a cross-compilation toolchain can be trickier than it looks.

"Our minimum version is whatever is available on Debian stable" is one way to break the deadlock.

Your internal teams might ask you to support a very old version of Rust. Over time, this will place an increasing burden on you. Your dependencies will eventually move towards the present, and you'll be stuck in the past. This will limit your ability to apply security patches and other critical upgrades. Therefore, you might expend fewer resources by assisting them to upgrade to a current version of Rust, rather than keeping your library working with the old one.

## details to add

This section describes some of the things that you should consider adding to your library.


### Linting policy
It's worth spending a little bit of time looking through the list of lints supported by the Rust compiler and Clippy, because you may wish to restrict some behaviours that are [allowed by default](https://doc.rust-lang.org/rustc/lints/listing/allowed-by-default.html).

In general, libraries built with a large intended audience should be built to higher standards. Accordingly, the suggestions below tend to increase the severity level of most lints.

Here is a suggested set of lints to add to libraries: 

```rust
#![deny(missing_docs)]
#![deny(missing_copy_implementations)]
#![deny(missing_debug_implementations)]
#![deny(clippy::perf)]
#![deny(improper_ctypes)]

#![warn(unit_bindings)]
#![warn(elided_lifetimes_in_paths)]
```

A discussion of the suggestions:

`#![deny(missing_docs)]`

This lint prevents you to from having public items without documentation. 

`#![deny(clippy::perf)]`

This lint protects against some patterns that are known to be slower than the suggested alternatives.

`#![warn(elided_lifetimes_in_paths)]`

This lint looks somewhat esoteric, but exists to ensure that 

```rust
struct Species(Arc<String>);

struct Tree<'a> {
	species: &'a Species,
}
```



**Warning: avoid `#![deny(warnings)]`**

Promoting warnings to errors could break your build at the next release of the Rust compiler (which happens every 6 weeks). Warnings are not stable between releases. If a new warning is introduced for code that compiles happily today, you'll require manual intervention when there's a version upgrade.

clippy lint polcy
Clippy provides a set of 

`cargo-vet` policy
https://mozilla.github.io/cargo-vet/

> When run, cargo vet matches all of a project's third-party dependencies against a set of audits performed by the project authors or entities they trust. If there are any gaps, the tool provides mechanical assistance in performing and documenting the audit.

A second-level of this is to check whether your team/company should contribute to the carg vet ecosystem. That is, if you invest your own resources to audit an external dependency, should you share your outcomes?

Action: contribute audits to cargo vet
Parties to consult: org leadership, OSPO, Legal, Security


`cargo-deny` policy




Machine-readable metadata. SPX headers.

File licence headers will help if you want your library to be included within the Debian repository.

Add a nix flake. Nix is a high-signal quality indicator.


## define help channels

Describe how you would like help requests to be sent to the project. Once you've written this down, can it be simplied? Think of the people who are lodging tickets, as well as people who are doing the triage.

Use the tools provided by your preferred environment. For example, if you use GitHub Issues, then configure its tracker to provide issue templates. Use workflows, but not too heavily. No one likes being forced to talk to a chatbot.

Once you've decided on how people should respond, you need to decide how to manage requests. Do you want to provide a public committment for responsiveness? What is the process for triage?

Ensure that a process for security issues is created. It may feel like overkill while you're just getting started, but you don't want to make it up during your first security incident.

While you may be small when you start out, remember that much of your goal is to create a strong first impression.  If it looks as though you have lots of processes in place, then you're likely to inspire the confidence. That confidence will lead to adoption. 

## consistency

Developers need their working memory. When you introduce a new paradigm, you create extra mental effort for those learning the tool. 

This often creates a conflict though. What if you're integrating with an external component that uses different conventions. 

Should you prioritise the user base of the existing tool or benefit newer people who are coming to the tool from the Rust side users? 

## documentation

If you ask people what they want from a library, they'll probably say that they want good documentation. But what they really want is a clear mental model of how your library works.

The documentation is one of the tools to establish that mental clarity, but it's not the only one and can only compensate for poor design.

Provide an indication of the the rationale and motivation for the library. It doesn't need to be long, but an indication of the philosophy behind it can make.

One of Microsoft Windows' software philosophies is a very strong stance on backwards compatibility. If this is one of your underlying principles, and your code originates at a time when previous styles were much more prevalent, then this may make it easier to explain why things feel clunkier.

Can you point to other uses of the library? No one wants to be the first to try something out.

#### show


#### more than API docs


a website
an 

### guidelines for docs

Technical writing is a skill. 

It's not enough for you to write what you intended. What's important is that people comprehend that as well.

#### Think about why someone is reading your document

Always have your reader's in mind while writing. 

Are they looking to solve a specific problem? 

#### Avoid the word "simple"

Don't tell users that something is "simple", "pretty easy" or similar. By the time they're reading your documentation, they're probably stressed. They don't want to be told that they're stupid.


## runnable examples

Hints that we're not doing the right thing. 

"Check the example directory"
"Check the tests"

## Examples of good libraries



## Case studies

### Random number generation

- [`rand_core`]
- [`rand`]
- [`rand_xorshift`]

[`rand_core`]: https://docs.rs/rand_core/latest/rand_core/index.html
[`rand_xorshift`]: : https://docs.rs/rand_xorshift

### tera
https://github.com/Keats/tera/tree/master
fuzzing 

### similiar
https://insta.rs/similar/

Lovely intro page. Nice use of an animated SVG as a screencast. Presumeably created with asciinema or similar.

### tinyvec
[https://docs.rs/tinyvec](https://docs.rs/tinyvec/latest/src/tinyvec/tinyvec.rs.html#98-103)

Interesting that they decide to use 2 spaces for indentation. Perhaps it's created by a Googler?

## References 

*Rust for Rustaceans* (2021). Jon Gjengset. No Starch Press.

"Towards Impeccable Rust" (2024). Jon Gjengset. Rust Nation UK. https://www.youtube.com/watch?v=qfknfCsICUM

# Tools List

#cargo 


| Tool (Link)                                         | Description | Example                                                             |
| --------------------------------------------------- | ----------- | ------------------------------------------------------------------- |
| [cargo-hack](https://github.com/taiki-e/cargo-hack) | Checks      | ```bash<br>cargo hack check --feature-powerset --no-dev-deps<br>``` |
|                                                     |             |                                                                     |


# Glossary

| Term     | Definition                                     |
| -------- | ---------------------------------------------- |
| manifest | The Cargo.toml file in the root of the project |
|          |                                                |
|          |                                                |

# Acknowledgements

Thank you to those who have supported this material's development

- Financial supporters of the the Rust Foundation Fellowship
- Predrag Gruevski for many comments and suggestions
