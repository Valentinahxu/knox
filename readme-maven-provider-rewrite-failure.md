Run build enviroment:

```bash
docker run --rm=true -t -i \
  -v "${TDP_HOME:-${PWD}}:/tdp" \
  -w "/tdp" \
  -v "${HOME}/.m2:/home/builder/.m2${V_OPTS:-}" \
  -e "BUILDER_UID=$(id -u)" \
  -e "BUILDER_GID=$(id -g)" \
  --ulimit nofile=500000:500000 \
  tdp-builder
```

Go under knox folder located in `components/apache-knox-current`.

Build lifecycle:

1. Validate:

Running on default `maven-enforcer-plugin.version=3.0.0-M3` leads to the failure of `gateway-server` module.
However build is sucessfull while `maven-enforcer-plugin.version=3.3.0`

```bash
mvn validate  -Prelease   -Ppackage   -Drat.numUnapprovedLicenses=1000   -DskipTests   -Dmaven.javdoc.skip=true   -Dcheckstyle.skip=true   -Dfindbugs.skip=true   -Dspotbugs.skip=true   --batch-mode 
```


2. Compile and Test

`gateway-applications` and `gateway-release` fail but run sucess when rerun with resume.

```bash
mvn compile  -Prelease   -Ppackage   -Drat.numUnapprovedLicenses=1000   -DskipTests   -Dmaven.javdoc.skip=true   -Dcheckstyle.skip=true   -Dfindbugs.skip=true   -Dspotbugs.skip=true   --batch-mode

mvn test  -Prelease   -Ppackage   -Drat.numUnapprovedLicenses=1000   -DskipTests   -Dmaven.javdoc.skip=true   -Dcheckstyle.skip=true   -Dfindbugs.skip=true   -Dspotbugs.skip=true   --batch-mode
```

```bash
-rf :gateway-applications
-rf :gateway-release
```

```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-dependency-plugin:3.1.2:unpack (unpack) on project gateway-applications: Artifact has not been packaged yet. When used on reactor artifact, unpack should be executed after packaging: see MDEP-98. -> [Help 1]

[ERROR] Failed to execute goal org.apache.maven.plugins:maven-dependency-plugin:3.1.2:unpack (unpack-services) on project gateway-release: Artifact has not been packaged yet. When used on reactor artifact, unpack should be executed after packaging: see MDEP-98. -> [Help 1]
```


4. Package

No failure using package.

```
mvn package  -Prelease   -Ppackage   -Drat.numUnapprovedLicenses=1000   -DskipTests   -Dmaven.javdoc.skip=true   -Dcheckstyle.skip=true   -Dfindbugs.skip=true   -Dspotbugs.skip=true   --batch-mode
```


5. Verify

```
mvn verify  -Prelease   -Ppackage   -Drat.numUnapprovedLicenses=1000   -DskipTests   -Dmaven.javdoc.skip=true   -Dcheckstyle.skip=true   -Dfindbugs.skip=true   -Dspotbugs.skip=true   --batch-mode
```

[ERROR] Failed to execute goal org.apache.maven.plugins:maven-dependency-plugin:3.1.2:analyze-only (analyze-dependencies) on project gateway-provider-rewrite: Dependency problems found -> [Help 1]


mvn verify  -Prelease   -Ppackage   -Drat.numUnapprovedLicenses=1000   -DskipTests   -Dmaven.javdoc.skip=true   -Dcheckstyle.skip=true   -Dfindbugs.skip=true   -Dspotbugs.skip=true   --batch-mode -rf :gateway-provider-rewrite


6. Install

```
mvn install  -Prelease   -Ppackage   -Drat.numUnapprovedLicenses=1000   -DskipTests   -Dmaven.javdoc.skip=true   -Dcheckstyle.skip=true   -Dfindbugs.skip=true   -Dspotbugs.skip=true   --batch-mode
```
























