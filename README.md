# build-tools

## Build Process

PREPARE > INSTALL > TEST > BUILD > RELEASE

## Common Includes

This project uses [build-tools-libs](https://github.com/javihgil/build-tools-libs) project for 
extending phing default features.

```xml
    <includepath classpath="tools" />
    
    <typedef name="modules" classname="lib.Phing.types.Modules" />
    <typedef name="module"  classname="lib.Phing.types.Module" />

    <taskdef name="call"    classname="lib.Phing.tasks.CallTask" />
    <taskdef name="ccopy"   classname="lib.Phing.tasks.CodeCopyTask" />
    <taskdef name="composr" classname="lib.Phing.tasks.ComposrTask" />
    <taskdef name="dev"     classname="lib.Phing.tasks.DevTask" />
    <taskdef name="info"    classname="lib.Phing.tasks.InfoTask" />
    <taskdef name="modulei" classname="lib.Phing.tasks.ModuleIteratorTask" />
    <taskdef name="package" classname="lib.Phing.tasks.PackageTask" />
    <taskdef name="rm"      classname="lib.Phing.tasks.RmTask" />
    <taskdef name="repo"    classname="lib.Phing.tasks.RepositoryTask" />
    <taskdef name="symfony" classname="lib.Phing.tasks.SymfonyTask" />
    <taskdef name="test"    classname="lib.Phing.tasks.TestTask" />
```

**Usage of common includes**

```xml
    <import file="tools/phing/_includes.xml"/>
```

## Common Configuration

```xml
    <property name="targetPath" value="target"/>
    
    <property name="module" value="false"/>

    <property name="phpunit.bin"    value="vendor/bin/phpunit"/>
    <property name="phplint.bin"    value="php"/>
    <property name="twiglint.bin"   value="vendor/bin/twig-lint"/>
    <property name="phploc.bin"     value="vendor/bin/phploc"/>

    <property name="phpcs.bin"      value="vendor/bin/phpcs"/>
    <property name="phpcs.standard" value="PSR2"/>

    <property name="test.phpunit" value="false" />
    <property name="test.phpunit.config" value="phpunit.xml"/>
    <property name="test.phpunit.failbuild" value="true"/>

    <fileset id="test.phpfiles" dir="/">
        <include name="**/*.php"/>
        <exclude name="vendor/**"/>
        <exclude name="${targetPath}/**"/>
        <exclude name="tools/**"/>
    </fileset>

    <property name="test.phplint" value="false" />
    <property name="test.phplint.failbuild" value="true"/>

    <property name="test.twiglint" value="false" />
    <property name="test.twiglint.failbuild" value="true"/>

    <fileset id="test.twigfiles" dir="/">
        <include name="Resources/**/*.twig" />
    </fileset>

    <property name="report.coverage" value="false" />

    <property name="report.phploc" value="false" />
    <property name="report.coverage.target" value="${targetPath}/reports/coverage"/>

    <property name="report.phpcs" value="false" />
    <property name="report.phpcs.report" value="${targetPath}/reports/phpcs-checkstyle.xml"/>
    <property name="report.phpcs.excludes" value="vendor/*,${targetPath}/*,tools/*,Tests/*"/>
```

**Usage of common configuration**

```xml
    <import file="tools/phing/_configuration.xml"/>
```

## Common Targets

**Usage of common targets**

```xml
    <import file="tools/phing/_common_targets.xml"/>
```

### init

This target initializes the build environment.

By default, this target exports some composer.json variables.
 
```xml
    <target name="init" hidden="true">
        <call target="-pre-init" required="false" />
        <echo message="Init project" />
        <composr action="export" value="name" property="composer.name" />
        <composr action="export" value="version" property="composer.version" />
        <composr action="export" value="type" property="composer.type" />
        <call target="-post-init" required="false" />
    </target>
```

**Extending**

Execute actions before default init actions.

```xml
    <target name="-pre-init">
        <!-- pre actions -->
    </target>
```

Execute actions after default init actions.

```xml
    <target name="-post-init">
        <!-- post actions -->
    </target>
```

Override init target.

```xml
    <target name="init">
        <!-- init actions -->
    </target>
```

### info

Shows info about the project.

This target has dependency on *init* target.
 
```xml
    <target name="info" depends="init">
        <call target="-pre-info" required="false" />
        <info show="property" property="composer.name"/>
        <info show="property" property="composer.version"/>
        <info show="property" property="composer.type"/>
        <call target="-post-info" required="false" />
    </target>
```

**Executing**

    $ phing info

**Extending**

Execute actions before default info actions.

```xml
    <target name="-pre-info">
        <!-- pre actions -->
    </target>
```

Execute actions after default info actions.

```xml
    <target name="-post-info">
        <!-- post actions -->
    </target>
```

Override init target.

```xml
    <target name="info" depends="init">
        <!-- info actions -->
    </target>
```

## Project Building

*TODO*

## Parent Building

This project is usefull for building more than one project from the same GIT repository.

**Project Structure**

    /.git/
    /module1/
        composer.json
        build.xml
    /module2/
        composer.json
        build.xml
    /tools/
    /composer.json
    /build.xml
    
Parent *build.xml* file must include the *tools/parent.xml* file, and define it's modules:

```xml
    <modules id="modules">
        <module name="module1"/>
        <module name="module2"/>
    </modules>
    
    <import file="tools/parent.xml"/>
```

Modules' *build.xml* files must extend other project configuration file (like *tools/project.xml*), 
and set that it's a module.

```xml
    <property name="module" value="true"/>
    
    <import file="../tools/project.xml"/>
```

### clear target

Executes *phing clear* in every child module, shows a summary and fails if some of the child fails.

**Execution**

    $ phing clear

### reset target

Executes *phing reset* in every child module, shows a summary and fails if some of the child fails.

**Execution**

    $ phing reset

### build target

Executes *phing build* in every child module, shows a summary and fails if some of the child fails.

**Execution**

    $ phing build

### report target

Executes *phing report* in every child module, shows a summary and fails if some of the child fails.

**Execution**

    $ phing report

### release target

Executes *phing release* in every child module, shows a summary and fails if some of the child fails.

After child releasing, tags the version in git repository, and prepares composer.json files for next
development iteration.

**Execution**

    $ phing release -Drelease.version=1.0.0 -Drelease.next.version=1.0.1

## Extending

Also is possible to override init target and use the default target without executing -pre and -post actions.

```xml
    <target name="info" depends="init">
        <!-- custom actions before default ones -->
        <call target="_common_targets.info" required="true" /> 
        <!-- custom actions after default ones -->
    </target>
```

