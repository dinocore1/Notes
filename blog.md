
### 2019-03-28

Today I learned that all *unstripped* android binaries are stored in the `$ANDROID_PRODUCT_OUT/symbols` dir. The binaries stored here are useful for debugging.

### 2019-03-26

Today I figured out the proper way to set the jenkins user inside a docker image. The Jenkinsfile gets the uid from the locally running node and stores it in `jenkins_user_id`. Then when it goes to build the docker image, it pass the uid as a build arg using `--build-arg JENKINS_UID=+jenkins_user_id`. This will always ensure that the jenkins user on the worker node is the same user in the docker image.

Dockerfile:
```
FROM ubuntu:18.04

ARG JENKINS_UID=99
ARG JENKINS_GID=99
ARG JENKINS_USER=jenkins
ARG JENKINS_GROUP=jenkins

RUN groupadd -g $JENKINS_GID $JENKINS_GROUP
RUN useradd -m -u $JENKINS_UID -g $JENKINS_GROUP $JENKINS_USER

USER $JENKINS_USER

RUN git config --global user.name "Jenkins" && \
    git config --global user.email "admin@videray.com"

```

Jenkinsfile:
```
def jenkins_user_id
def jenkins_group_id

node {
    jenkins_user_id = sh(returnStdout: true, script: 'id -u').trim()
    jenkins_group_id = sh(returnStdout: true, script: 'id -g').trim()
}

pipeline {
    agent {
        dockerfile {
            label 'large && linux'
            additionalBuildArgs '--build-arg JENKINS_UID=' + jenkins_user_id + ' --build-arg JENKINS_GID=' + jenkins_group_id
        }
    }

    stages {
        stage('Build') {
            steps {
                sh 'cd /build && repo sync --force-sync'
                sh 'cd /build && ./aosp_build.sh'
            }
        }
}
```

### 2019-03-22

Today I discovered the android apps that use JNI libraries to link to system libs will get a runtime error. I was running into this problem.

```
java.lang.UnsatisfiedLinkError: dlopen failed: library "libcutils.so"
("/system/lib/libcutils.so") needed or dlopened by "/system/lib/libnativeloader.so"
is not accessible for the namespace "classloader-namespace"
  at java.lang.Runtime.loadLibrary0(Runtime.java:977)
  at java.lang.System.loadLibrary(System.java:1602)
```

With further research, I discovered that this was a [new feature](https://android-developers.googleblog.com/2016/06/improving-stability-with-private-cc.html). Starting in android 7.0, you need to declare [native library namespace](https://source.android.com/devices/tech/config/namespaces_libraries) to allow regular apps to use them. You do this by adding a `/vendor/etc/public.libraries.txt` and listing the names of the public libs within it. *Note*: this rule does not apply to any app signed with the "platform".

