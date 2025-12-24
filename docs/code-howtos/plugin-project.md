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
       implementation("org.jabref:jablib:<release-version>")
   }
   ```

   Replace `<release-version>` with a version available on Maven Central (see <https://search.maven.org/artifact/org.jabref/jablib>).
3. If your project uses the Java module system, add `requires org.jabref.jablib;` to your `module-info.java`.

## Minimal Maven setup

```xml
<dependency>
  <groupId>org.jabref</groupId>
  <artifactId>jablib</artifactId>
  <version>REPLACE_WITH_RELEASE_VERSION</version>
</dependency>
```

## Working against local JabRef sources

When you also develop JabRef itself and want to consume the current sources without publishing artifacts, use a Gradle composite build:

1. Keep your JabRef clone available locally (for example `/home/runner/work/jabref/jabref`).
2. In your plugin project's `settings.gradle.kts`, add:

   ```kotlin
   includeBuild("/home/runner/work/jabref/jabref")
   ```

3. Depend on the local module in `build.gradle.kts`:

   ```kotlin
   dependencies {
       implementation(project(":jablib"))
   }
   ```

4. Build with `./gradlew --include-build /home/runner/work/jabref/jabref build` so Gradle wires your project to the JabRef modules automatically.

## Where to hook into functionality

- `jablib` exposes the logic and model layers (e.g., `BibEntry`, import/export, citation key generation). See the samples in [`jablib-examples`](../../jablib-examples) for working code.
- Features that modify the JabRef UI (menus, side panes, actions) must be added directly to the `jabgui` module; there is currently no plug-in loader inside the desktop app.
- For command-line integrations, you can also look at the `jabkit` module, which uses the same `jablib` APIs to build CLI commands.
