# build-tools

## Build Process

PREPARE > INSTALL > TEST > BUILD > RELEASE

## Common Includes

This project uses [build-tools-libs](https://github.com/javihgil/build-tools-libs) project for 
extending phing default features.

**Usage of common includes**

```xml
    <import file="tools/phing/_includes.xml"/>
```

## Common Configuration

Defines common configuration for some targets.

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
    <?xml version="1.0" encoding="UTF-8"?>
    <project name="project-name" default="info">
        <modules id="modules">
            <module name="module1"/>
            <module name="module2"/>
        </modules>
        
        <includepath classpath="tools" />
        <import file="tools/parent.xml"/>
    </project>
```

Modules' *build.xml* files must extend other project configuration file (like *tools/project.xml*), 
and set that it's a module.

```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project name="module-name" default="info">
        <property name="module" value="true"/>
        
        <includepath classpath="../tools" />
        <import file="../tools/project.xml"/>
    </project>
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

