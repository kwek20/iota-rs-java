# iota-rs-java
Example use of [iota.rs](https://github.com/iotaledger/iota.rs) Java bindings

## Pre-requirements
- Download or clone the `iota.rs` repository and navigate to the java bindings folder.
```
$ git clone https://github.com/iotaledger/iota.rs.git
```

- A valid C ompiler
- [Rust](https://www.rust-lang.org/tools/install) installation on your path
- Gradle or Maven installed

In order to build with the Java bindings, you need the following two parts:
1. Java classes calling those `native` methods (`.jar`)
2. JNI bindings linking `Rust` to `C`, and then `C` to java `native` methods (`.so` , `.dll` or `.dylib` depending on your system)


# Step 1: Linking the Java file (Jar)
## Maven

### Installing the jar on your system
```bash
mvn install:install-file -Dfile=/path/to/iota.rs/bindings/java/native/build/libs/native.jar -DgroupId=org.iota.client -DartifactId=native -Dversion=1.0 -Dpackaging=jar 
```

### Adding the dependency to POM

```java
<dependency>
    <groupId>org.iota.client</groupId>
    <artifactId>native</artifactId>
    <version>1.0<version>
</dependency>
```

## Gradle
***With a pre-made jar***
- Point to the JAR in `build.gradle` `dependencies` section using `implementation files("native.jar")`

Building the jar through gradle in `iota.rs` creates the jar at `iota.rs/bindings/java/native/build/libs`

***Directly pointing to the iota.rs project***
- Uncomment the lines in `settings.gradle`, then:
- Change `settings.gradle` to point to the `\native` project inside `iota.rs`, so we can load the Java files
- Add `implementation project(':native')` to the `dependencies` section of your `build.gradle`

# Step 2: Adding the native library

This file can be found at `iota.rs/bindings/java/target/release` after building the bindings with `cargo build --release` in the `iota.rs/bindings/java` folder. We will call this file `iota_client` for the purpose of this README.

## Generic

Adding the folder to your PATH is the easiest way to ensure it is available.

### Linux
1. Change to your home directory. `cd $HOME`.
2. Open the `.bashrc` file.
3. Add the following line to the file: `export PATH=/path/to/iota_client:$PATH`.
4. Save the file and exit. Use the `source` command to force Linux to reload the `.bashrc`

### Windows
1. Open the Start Search, type in “env”, and choose “Edit the system environment variables”
2. Click the “Environment Variables…” button.
3. Under the “System Variables” section (the lower half), find the row with “Path” in the first column, and click edit.
4. The “Edit environment variable” UI will appear. Click “New” and type in the new path: `/path/to/iota_client`
5. Dismiss all of the dialogs by choosing “OK”. Your changes are saved!

### OSX
1. Open up Terminal.
2. Run the following command: sudo nano /etc/paths.
3. Enter your password, when prompted.
4. Go to the bottom of the file, and enter the path you wish to add.
5. Hit control-x to quit.
6. Enter “Y” to save the modified buffer.

## Maven
We need to make sure the file is added to `java.library.path` before building or running.
To do this, we add/update the section below to our `pom.xml`

The `${basedir}` means we need to place the `iota_client` file in the base of our repo. (Where your pom.xml is)
Alternatively, you can replace `${basedir}` with an absolute path to the file: `/path/to/iota_client` 

```java
<build>
    <plugin>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>[VERSION]</version>
        <configuration>
            <argLine>-Djava.library.path=${basedir}</argLine>
        </configuration>
    </plugin>
</build>
```

## Gradle

Modify `build.gradle` variable `iotaLibLocation` to the location of `iota_client`.

# Step 3. Building your app

## Maven
Run `mvn compile` to build.

Run `mvn exec:java -Dexec.mainClass="org.example.ExampleApp"` to run. (You can also add the mainclass to your pom using the `exec-maven-plugin` plugin)

Run `mvn test` to specifically run the test.

## Gradle
Run `gradle build` to build.

Run `gradle run` to run. (linking directly to the iota.rs for jar triggers a rerun every time)

Run `gradle test` to specifically run the test.

# Documentation
As the API is made to be as close as possible to the rust API, the most up to date documentation can be found [here](https://client-lib.docs.iota.org/docs/libraries/rust/getting_started), until a pure Java version is made.

YOu can also generate more up-to-date documentation by compiling it yourself. Instructions can be found [here]](https://github.com/iotaledger/iota.rs/tree/dev/documentation#readme) 

The java methods are made to almost 1:1 correspond to rust version. Difference beeing the naming convention (Rust beeing snake_case, java camelCase)

# Limitations

Due to the fact that we are linking through C from Rust, there are a couple of limiting factors.

- Classic builder patterns return a `clone` after each builder call since we can only pass back to C by reference in `Rust`
```Java
Builder builder1 = new Builder();
Builder builder2 = builder1.setValue(true);

// These are different instances, thus builder1 wont have the value set
assertNotEquals(builder1, builder2);
```