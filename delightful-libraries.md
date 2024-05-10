# Advice for writing software libraries in Rust

- Author: Tim McNamara, @timClicks. <tim@mcnamara.nz>
- Licence: CC-BY-SA 4.0
- Version: 0.0.0
- Status: VERY INCOMPLETE


:::info
This document has been created as part of my Rust Fellowship for the June 2023-2024 year. Thank you to all of the sponsors who have provided financial support for the program and to the foundation for facilitating it. 
:::

# Introduction

If you're writing a library, then you are writing software to be helpful. You're absorbing the cost of writing code and relieving that burden from others. Thank you.

You should expect that people see your software as a risk. Every dependency adds some weight to their project. Your primary goal is to provide a good first impression. Although this seems superficial, to create a good first impression, everything needs to be in place.

Most developers that use your crate will be novice Rust programmers. This is likely to remain so for many years. Rust is growing very rapidly. This means that there will always be a large cohort of users who will get stuck.

Every new tool imposes a productivity burden on its adopters. We look to new things to speed things up. However, when we're learning, we slow down.

Most developers are busy and distracted. They don't want to have to think too much. Your code should fit with patterns that they already understand. Your documentation should explain how.

Most developers are opinionated and fussy. Computer programming is not an easy skill to acquire. To become proficient, programmers need to learn how to push through adversity and muddle their way through.

Avoid magic. It's rarely what people want to see. Once people understand traits, most magic comes from macros.

Use cargo. Some businesses have their own build systems. It's generally a better idea to teach the build system about cargo, rather than teaching it to compile Rust directly. The cargo ecosystem is very rich and it contains many plugins. Avoiding them has a large cost.

It's true that Cargo enforces semantic versioning. However it's also true that, Cargo imposes additional constraints upon Rust projects than what the semantic versioning spec actually requires. Strict compliance with semantic versioning means that there is no API stability when the library has not reached 1.0. However, the Rust community expects that pre-1.0 libraries to treat their minor version number as a major version number. When a breaking change is introduced in a patch version, [people complain](https://twitter.com/5422m4n/status/1785301639739412492) .

Another aspect of Rust's semantic versioning culture is that it's very hard for projects to reach 1.0. Once they're there, it's very difficult to reach the next major version.

If you have a popular library, there is little political capital for you to introduce breaking changes.


# Documentation

The project's documentation is how it makes its first impression. Sending people to the `examples` directory is not enough. Yes, developers can look through the code, and they will if they're already invested in the project, but they won't want to wade through your source code to get a dist of what's happening.

Distinguish between 

The majority of people reading your documentation will not be native English speakers. This does not mean that they're unable to speak the language, but it does mean that you should be wary if you use uncommon phrases and idioms. 

The general advice about written communication also applies to technical documentation. Language is surprisingly difficult to do at scale. There is a lot of context missing when we write.

When writing, consider the audience's needs. A common problem is expert blindness. We forget what things look like as a beginner. Because we know the tools so well, we don't need to consciously think about which steps we need to take, therefore it's hard to explicitly state every step.

Ask others to read through your tutorial. If you're feeling brave, you can also do this over a video call to get immediate feedback about things that are confusing.
## Reference

API documentation is a n

I dislike cute examples because they often . Likewise, using Foo and Barr as variable names 
## tutorial

A tutorial introduction is often appreciated. This will guide people through the process of what setup needs to occur (e.g. )

Examples: `regex`, `clap`, and a number of parsing libraries such as [`chumsky`]

`regex`: 
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



### Release profile
Cargo supports the notion of a "profile". Each profile has a different configuration settings.

In general, the release profile takes the most time to compile, but programs that are built with it run the fastest.

Recommended settings.

```toml
[profile.dev]
opt-level = 3

[profile.release]
opt-level = 3
debug = true
lto = "thin" # consider 1,
panic = "abort"
```

- `debug` symbols are very useful when you need them. Consider 

#### Consider a "dist" profile for public distribution

If you're creating a version of your library for public consumption, and your code isn't open source, then you may wish to create a dedicated build profile for distribution.

Cargo profile. Consider LTO. For gather data, you need profiling and benchmarks.



Information hiding

Using a `pub` field means that you've exposed your internal representation and have committed to maintaining its presence until the next major version.

Traits and preludes

One of the biggest issues that you'll face as a library author, especially with beginning Rust programmers, is that they'll forget to import a trait. Then methods won't be available on types and the error messages will be cryptic. Users will copy + paste from the HTML documentation, but the examples in the docs hide the imports.

There are a few ways around this. One is to use fewer traits. Another is to use a prelude, so that it's harder to forget traits. A third, less obvious, method is to ensure that the `use` statement is at the top of the code examples. `rustup`


Feature gates. 

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

## Technique: increasing build speed

The only way to go faster is to do less work.
### Split large crates into pieces

Smaller crates build faster and experience less churn during development. A crate is the "unit of compilation" within the Rust ecosystem, meaning that crates can be built independently in parallel. When you're making code changes, you typically are making changes to one place. Therefore, Rust won't need to recompile the whole project.   
### Use a single version of each dependency

Check whether you're building multiple versions of a library in your dependency graph.
 
 Where possible, upgrade the older versions so that only a single version is needed.
 
Upgrading upstream might require sending a PR upstream for them to upgrade their version in their code. If they're slow at approving it, you can 
[override upstream ](https://doc.rust-lang.org/nightly/cargo/reference/overriding-dependencies.html#the-patch-section) by using the `[patch]` functionality within Cargo to replace a version of upstream with a version that you control. The ["Testing a bugfix" section](https://doc.rust-lang.org/nightly/cargo/reference/overriding-dependencies.html#testing-a-bugfix) of the Overriding Dependencies chapter of the Cargo Book details this process.

Sometimes, you may pin an old version of a dependency to support compilation with an older version of Rust. 

### Add feature flags to your library

Feature flags are a good way to facilitate speeding up builds. 
### Limit features from your dependencies

When your library is nearing stability, it can be a good idea to start pruning the features that you need from your dependencies. As an example, `tokio` and `regex` both come with many features that are not necessary for all projects.

The regex crate is a good example of a crate that's quite demanding on the CPU

## Technique: hiding methods that need to be public for testing

One of the unfortunate aspects of Rust's testing regime is that it can require you to expose your internal API.

You have a few strategies to to mitigate this.

The easiest is to annotate your function with `#[doc(hidden)]`. This'll make it hard to find.

Something that's might be worthwhile to do is to mark the function as deprecated. That will generate a loud warning whenever someone compiles their code. Sadly, the compiler will also nag you.

A related option is to mark the function as `unsafe`. This generates a similar anxiety-laden 

```rust
#[doc(hidden)]
pub unsafe fn _public_for_testing_only() {
    // ...
}
```


## Technique: making it impossible to call an internal method, aka sealing a trait

 
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

# Projects

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



This incud


### Avoid "params structs"



### Use standard formatting

Use `rustfmt`


### release hygiene

- check for outdated dependencies (`cargo outdated -d 1`)
- check for security issues (`cargo audit`)
- check against all features and all targets (`cargo hack check --feature-powerset --no-dev-deps`)



ci/cd.

semantic versioning breakages - `cargo semver-checks`. 

use automation where possible, e.g. `cargo release` or `release-plz`, and checklists where that's impossible.

Make your code auditable. The hash of git tag of your release should match the hash of what's uploaded to crates.io.

if your code accepts input from the outside world, you should include fuzz testing.

## reducing the number of dependencies

Consider specialized dependencies. For example, although `serde` is by far the most common serialization crate, it's by no means the only option. It's quite bulky and is somewhat expensive to compile. You could consider `rkyv` (pronounced archive), for example.

Where possible, move dependencies to `[build-dependencies]` and `[dev-depdencies]`.

Try to reduce the number of features that you're including from upstream. For your dependencies, is it possible to build with a subset of the crate, e.g. `cargo --no-default-features --features <feature>...` ? If so, then do it! Pruning as much away from build time will increase the speed for everyone.

At least, being prudent with features means that it's less likely that you'll end up negatively impacting others' build times. Cargo will take the set of all features. So if one of your dependencies' dependencies still uses a feature that you've opted out of, your build will still include it.

# For projects being written in large enterprises

If you're a professional developer within a large enterprise, the expectations are higher. 

You also have many more resources though, so hopefully things even out. One of the advantages you will have is access to more compute resources. Ensure that your testing setup is robust.

Consider advanced approaches, such as property-based testing, fuzzing and mutation testing.

## project management

Lots of the difficulty in open source relates to the mechanics of getting people to work together. Coordination is very difficult. Open source spans many boundaries, such as organisational and cultural.  

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

## Limit exporting others crates 


Practicality can be 

## Minimum stable Rust version



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



