# iota-rs-java
Example use of [iota.rs](https://github.com/iotaledger/iota.rs) Java bindings

## Pre-requirements
- Download or clone the `iota.rs` repository and navigate to the java bindings folder.
```
$ git clone https://github.com/iotaledger/iota.rs.git
```

- A valid C ompiler
- [Rust](https://www.rust-lang.org/tools/install) installation on your path

## Preparing gradle

In order to build with the Java bindings, you need the following two parts:
- JNI bindings linking `Rust` to `C`, and then `C` to java `native` methods
- Java classes calling those `native` methods



**Linking the JNI bindings**

Modify `build.gradle` variable `iotaLibLocation` to the location of the iota.rs library file.

This file can be found at `iota.rs/bindings/java/target/debug` after building the bindings with `cargo build` in the `iota.rs/bindings/java` folder

**Linking the Java file (Jar)**

***With a pre-made jar***
- Point to the JAR in `build.gradle` `dependencies` section using `implementation files("native.jar")`

Building the jar through gradle in `iota.rs` creates the jar at `iota.rs/bindings/java/native/build/libs`

***Directly pointing to the iota.rs project***
- Uncomment the lines in `settings.gradle`, then:
- Change `settings.gradle` to point to the `\native` project inside `iota.rs`, so we can load the Java files
- Add `implementation project(':native')` to the `dependencies` section of your `build.gradle`

## Building your app

Run `gradle build` to build.

Run `gradle run` to run. (linking directly to the iota.rs for jar triggers a rerun every time)

Run `gradle test` to specifically run the test.

## Documentation
As the API is made to be as close as possible to the rust API, the most up to date documentation can be found [here](https://client-lib.docs.iota.org/libraries/rust/index.html), until a pure Java version is made.

The java methods are made to almost 1:1 correspond to rust version. Difference beeing the naming convention (Rust beeing snake_case, java camelCase)

## Limitations

Due to the fact that we are linking through C from Rust, there are a couple of limiting factors.

- Classic builder patterns return a `clone` after each builder call since we can only pass back to C by reference in `Rust`
```Java
Builder builder1 = new Builder();
Builder builder2 = builder1.setValue(true);

// These are different instances, thus builder1 wont have the value set
assertNotEquals(builder1, builder2);
```
- 