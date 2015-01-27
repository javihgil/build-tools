# build-tools

These tools are thought for implement an automated build and release process for PHP projects 
in Linux systems using phing.

## Usage

Execute in git repository root:

    $ git submodule add https://github.com/javihgil/build-tools.git tools
    $ git submodule update --init --recursive

## Build Process

PREPARE > INSTALL > TEST > BUILD > RELEASE

## Common Includes

This project uses [build-tools-libs](https://github.com/javihgil/build-tools-libs) project for 
extending phing default features.

**Usage of common includes**

```xml
    <import file="tools/phing/includes.xml"/>
```

## Common Configuration

Defines common configuration for some targets.

**Usage of common configuration**

```xml
    <import file="tools/phing/configuration.xml"/>
```

## Common Targets

**Usage of common targets**

```xml
    <import file="tools/phing/common_targets.xml"/>
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

## Composer Project Building

### clear target

Clears project. Removes vendor, reports, build and release directories.

**Depends on**

- common init target

**Execution**

    $ phing clear

### reset target

Clears project. Removes target directory, composer's phar and lock files.

**Depends on**

- clear target

**Execution**

    $ phing reset

### prepare target

Prepares project for other target execution. Creates target directory and downloads (or self updates) 
composer.phar file.

**Depends on**

- clear target

**Execution**

This is a *hidden* target, but can be executed using:

    $ phing prepare

### install target

Executes composer install command. 

It's possible to download previous some packages from private repository.

**Depends on**

- prepare target

**Execution**

    $ phing install

**Usage of private repositories**

    <property name="private.repository" value="true" />
    <property name="private.repository.driver" value="s3" /> <!-- available drivers: s3 -->
    <property name="private.dependencies.regex" value="^javihgil\/"/>
    <property name="s3.build.bucket.path" value="mybucket/builds"/>
    <property name="s3.release.bucket.path" value="mybucket/releases"/>
    <property name="s3.cmd.bin" value="/usr/bin/s3cmd"/>

### test target

Tests project. 

**Depends on**

- install target

**Execution**

    $ phing test

**PHPunit test configuration**

    <property name="test.phpunit" value="true" />
    
Default configuration
    
    <property name="phpunit.bin" value="vendor/bin/phpunit"/>
    <property name="test.phpunit.config" value="phpunit.xml"/>
    <property name="test.phpunit.failbuild" value="true"/>

Include [phpunit](https://github.com/sebastianbergmann/phpunit) in composer.json
    
```json
    "require-dev": {
        "phpunit/phpunit": "4.2.*"
    },
```

**PHP Lint test configuration**

    <property name="test.phplint" value="true" />
    
Default configuration
    
    <property name="phplint.bin" value="php"/>
    <property name="test.phplint.failbuild" value="true"/>

**Twig Lint test configuration**

    <property name="test.twiglint" value="true" />
    
Default configuration
    
    <property name="twiglint.bin" value="vendor/bin/twig-lint"/>
    <property name="test.twiglint.failbuild" value="true"/>

Include [twiglint](https://github.com/asm89/twig-lint) in composer.json
    
```json
    "require-dev": {
        "asm89/twig-lint": "1.0.*"
    },
```

### report target

Tests project. 

**Depends on**

- install target

**Execution**

    $ phing report

**PHPunit report configuration**

    <property name="report.coverage" value="true" />
    
Default configuration
    
    <property name="phpunit.bin" value="vendor/bin/phpunit"/>
    <property name="test.phpunit.config" value="phpunit.xml"/>
    <property name="report.coverage.target" value="${targetPath}/reports/coverage"/>

Include [phpunit](https://github.com/sebastianbergmann/phpunit) in composer.json
    
```json
    "require-dev": {
        "phpunit/phpunit": "4.2.*"
    },
```

**PHPloc report configuration**

    <property name="report.phploc" value="true" />
    
Default configuration
    
    <property name="phploc.bin" value="vendor/bin/phploc"/>
    <property name="report.phploc.report" value="${targetPath}/reports/phploc.csv"/>
    <property name="report.phploc.excludes" value="vendor,${targetPath},tools"/>

Include [phploc](https://github.com/sebastianbergmann/phploc) in composer.json
    
```json
    "require-dev": {
        "phploc/phploc": "2.0.*"
    },
```

**PHPCS report configuration**

    <property name="report.phpcs" value="true" />
    
Default configuration
    
    <property name="phpcs.bin" value="vendor/bin/phpcs"/>
    <property name="phpcs.standard" value="PSR2"/>
    <property name="report.phpcs.report" value="${targetPath}/reports/phpcs-checkstyle.xml"/>
    <property name="report.phpcs.excludes" value="vendor/*,${targetPath}/*,tools/*,Tests/*"/>

Include [phploc](https://github.com/sebastianbergmann/phploc) in composer.json
    
```json
    "require-dev": {
        "squizlabs/php_codesniffer": "2.1.*"
    },
```

### build target

*TODO*

### release target

*TODO*

### release-next-version target

*TODO*

## Parent Building

This project is useful for building more than one project from the same GIT repository.

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
        
        <property name="toolsPath" value="tools"/>
        <import file="${toolsPath}/parent.xml"/>
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
        <call target="common_targets.info" required="true" /> 
        <!-- custom actions after default ones -->
    </target>
```

## Install latest s3cmd on Ubuntu
 
[Source](https://gist.github.com/dominicsayers/5100711)

    sudo apt-get install python-setuptools python-dateutil
    wget http://downloads.sourceforge.net/project/s3tools/s3cmd/1.5.0/s3cmd-1.5.0.tar.gz
    tar xvfz s3cmd-1.5.0.tar.gz
    cd s3cmd-1.5.0
    sudo python setup.py install
    s3cmd --vesion
