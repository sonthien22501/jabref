---
parent: Code Howtos
---
# Creating a companion plugin project

JabRef does not expose a runtime plugin API inside the desktop application. The supported way to build extensions is to create a separate project that depends on JabRef's published library module `jablib`, which contains the model and logic packages.

## Minimal Gradle setup

1. Create a new project (for example with `gradle init --type java-application`).
2. Add the `jablib` dependency:

   ```kotlin
   repositories {
       mavenCentral()
   }

   dependencies {
       implementation("org.jabref:jablib:LATEST_VERSION") // for example 5.13.0
   }
   ```

   Replace `LATEST_VERSION` with the newest version available on Maven Central (see <https://search.maven.org/artifact/org.jabref/jablib>).
3. If your project uses the Java module system, add `requires org.jabref.jablib;` to your `module-info.java`.

## Minimal Maven setup

```xml
<dependency>
  <groupId>org.jabref</groupId>
  <artifactId>jablib</artifactId>
  <version>LATEST_VERSION</version> <!-- for example 5.13.0 -->
</dependency>
```

## Working against local JabRef sources

When you also develop JabRef itself and want to consume the current sources without publishing artifacts, use a Gradle composite build:

1. Keep your JabRef clone available locally (for example `/path/to/jabref`).
2. In your plugin project's `settings.gradle.kts`, add:

   ```kotlin
   includeBuild("/path/to/jabref")
   ```

3. Depend on the local module in `build.gradle.kts`:

   ```kotlin
   dependencies {
       implementation(project(":jablib"))
   }
   ```

4. Build with `./gradlew build`. If you prefer not to touch `settings.gradle.kts`, skip step 2 and run `./gradlew --include-build /path/to/jabref build` to wire in the checkout for a single build.

## Where to hook into functionality

- `jablib` exposes the logic and model layers (e.g., `BibEntry`, import/export, citation key generation). See the samples in [`jablib-examples`](https://github.com/JabRef/jabref/tree/main/jablib-examples) for working code.
- Features that modify the JabRef UI (menus, side panes, actions) must be added directly to the `jabgui` module; there is currently no plugin loader inside the desktop app.
- For command-line integrations, you can also look at the `jabkit` module, which uses the same `jablib` APIs to build CLI commands.
