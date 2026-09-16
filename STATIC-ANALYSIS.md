# Static Analysis Mini Project: SpotBugs + Find Security Bugs on WebGoat

This checkout is WebGoat **v2023.8** (chosen because it targets Java 17, matching
the lab machines) with the `spotbugs-maven-plugin` wired into `pom.xml`,
configured with the OWASP **Find Security Bugs** plugin.

## Prerequisites

- JDK 17
- Maven 3.6.3+ (or just use the bundled wrapper `mvnw` / `mvnw.cmd`, no local
  Maven install required)

## 1. Run WebGoat itself

```Shell
# Windows
$env:JAVA_HOME = "C:\Program Files\Java\jdk-17"
.\mvnw.cmd spring-boot:run

# Linux/Mac
./mvnw spring-boot:run
```

Then open http://localhost:8080/WebGoat and create an account.

## 2. Run the static analysis

Generate the raw XML report (`target/spotbugsXml.xml`):

```Shell
mvn com.github.spotbugs:spotbugs-maven-plugin:4.10.3.0:spotbugs
```

Fail the build if bugs are found (useful to show a "gate" in CI):

```Shell
mvn com.github.spotbugs:spotbugs-maven-plugin:4.10.3.0:check
```

Open the interactive desktop viewer (browse findings, jump to source,
read the CWE-mapped description for each bug) — needs a display, run it
from your own terminal, not headless CI:

```Shell
mvn com.github.spotbugs:spotbugs-maven-plugin:4.10.3.0:gui
```

## 3. What to look at

`category='SECURITY'` findings come from Find Security Bugs and map to real
CWEs. On this checkout that includes things like:

- `SQL_INJECTION_JDBC` (CWE-89)
- `PATH_TRAVERSAL_IN` (CWE-22)
- `COMMAND_INJECTION` (CWE-78)
- `CRLF_INJECTION_LOGS` (CWE-117)
- `PREDICTABLE_RANDOM` (CWE-330)

Each `<BugInstance>` in the XML report has a `cweid` attribute plus a
`<SourceLine>` pointing at the exact file/line, so you can cross-reference a
finding against the actual WebGoat lesson code that intentionally contains
the vulnerability.

## Mini project ideas

1. Pick 3-5 `SECURITY`-category findings, trace each to the WebGoat lesson
   file it's in, and explain whether it's a true positive (the code really is
   exploitable) or a false positive.
2. Compare SpotBugs' plain output vs. Find Security Bugs enabled — rerun with
   the `<plugins>` block removed from the `spotbugs-maven-plugin`
   configuration in `pom.xml` and diff the bug counts.
3. Map found CWEs to the OWASP Top 10 categories and produce a short report.
