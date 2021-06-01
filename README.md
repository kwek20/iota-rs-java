# iota-rs-java
Example use of iota.rs Java bindings

## Preparing gradle

**Linking the JNI bindings**

Modify `build.gradle` variable `iotaLibLocation` to the location of the iota.rs library file.

This file can be generated at `iota.rs/bindings/java/target/debug`

**Linking the Java file (Jar)**

***With a pre-made jar***
- Point to the JAR in `build.gradle` `dependencies` section using `implementation files("native.jar")`

Building the jar through gradle in `iota.rs` creates the jar at `iota.rs/bindings/java/native/build/libs`

***Directly pointing to the iota.rs project***
- Uncomment the lines in `settings.gradle`, then:
- Change `settings.gradle` to point to the `iotaLibLocation` with `\native` appended so we can load the Java files
- Add `compile project(':native')` to the `dependencies` section of your `build.gradle`

## Building your app

Run `gradle build` to build.

Run `gradle run` to run. (linking directly to the iota.rs for jar triggers a rerun every time)

Run `gradle test` to specifically run the test.
