# build-tools

## Build Process

PREPARE > INSTALL > TEST > BUILD > RELEASE

## Common Includes

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

## Parent Building





