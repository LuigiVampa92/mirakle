# Mirakle
A Gradle plugin that allows you to move build process from a local machine to a remote one.

Compatible with Gradle 4.0+. Works seamlessly with IntelliJ IDEA and Android Studio.

#### Why
Remote machine supposed to be much performant than you working machine.
Also having a sufficient network bandwidth or small amount of data that your build produce, you gain **build speed boost**.

#### How it differs from [Mainframer](https://github.com/buildfoundation/mainframer)

Mirakle is designed specially for Gradle build system. It works as seamless as possible. Once plugin installed, you workflow will not be different at all.

(It's a good thing to prank your colleague. Imagine his surprise when one day he get several times faster build time.)


## Setup
* Put this into `USER_HOME/.gradle/init.d/mirakle_init.gradle` on local machine
```groovy
initscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath "com.instamotor:mirakle:1.4.0"
    }
}
 
apply plugin: Mirakle
 
rootProject {
    mirakle {
        host "your_remote_machine"
    }
}
```
* [Setup local machine](docs/SETUP_LOCAL.md)
* [Setup remote machine](docs/SETUP_REMOTE.md)
* [Tips and Tricks](docs/TIPS_AND_TRICKS.md)

## Usage
```
> ./gradlew build

Here's Mirakle. All tasks will be executed on a remote machine.
:uploadToRemote
:executeOnRemote
...remote build output...
:downloadFromRemote
 

BUILD SUCCESSFUL
 
Total time : 7.975 secs
Task uploadToRemote took: 2.237 secs
Task executeOnRemote took: 3.706 secs
Task downloadFromRemote took: 1.597 secs
```

To disable remote execution pass `-x mirakle`

```
> ./gradlew build install -x mirakle
```

#### Opt-in execution
By default Mirakle is executing everytime unless `-x` is provided. To reverse this behaviour add `if` statement to init script:

```
if (startParameter.taskNames.contains("mirakle")) {
    apply plugin: Mirakle

    rootProject {
        mirakle {
            ...
        }
    }
}
```
```
> ./gradlew heavyBuild mirakle
```

## Configuration
```groovy
mirakle {
    host "true-remote"
    
    //optional
    remoteFolder ".mirakle"
    excludeLocal += ["foo"]
    excludeRemote += ["bar"]
    excludeCommon += ["**/foobar"]
    rsyncToRemoteArgs += ["--stats", "-h"]
    rsyncFromRemoteArgs += ["--compress-level=5", "--stats", "-h"]
    sshArgs += ["-p 22"]
    fallback true
    downloadInParallel true
    downloadInterval 2000
    breakOnTasks = ["install", "package"] // regex pattern
}
```
### Per-project config
You can configure Mirakle differently for any project. There's two ways:
1. In `USER_HOME/.gradle/init.d/mirakle_init.gradle`
```groovy
initscript { .. }
 
apply plugin: Mirakle
 
rootProject {
    if (name == "My Home Project") {
        mirakle {
            host "my-build-server"
            sshArgs += ["-p 222"]
        }
    } else {
        mirakle {
            host "office-build-server"
        }
    }
}
```
2. In `PROJECT_DIR/mirakle.gradle`. This is useful if you want too add the config to VCS.
```groovy
mirakle {
    host "my-build-server"
    sshArgs += ["-p 222"]
}
```
**Note:** `mirakle.gradle` is the only Gradle build file in the project dir which is evaluated on local machine. Other build files are ignored for the sake of saving time.

##### [Mainframer config](https://github.com/buildfoundation/mainframer/blob/v2.1.0/docs/CONFIGURATION.md) is also supported.

## [Contributing](docs/CONTRIBUTING.md)

## License
```
Copyright 2017 Instamotor, Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
