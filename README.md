# build-tools

## Build Process

PREPARE > INSTALL > TEST > BUILD > RELEASE

## Common Includes

## Common Configuration

## Common Targets

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

## Parent Building





