# nextfractal-openjdk-binaries

Since there are many flavours of Java nowadays, NextFractal started providing an embedded Java JDK to ensure that users always have the correct Java runtime and libraries. This project provides the correct JDK images required for assembling NextFractal's distribution package for Mac, Linux, and Window.

The binaries are currently based on OpenJDK 11 available on https://github.com/AdoptOpenJDK/openjdk-jdk11.


## How to create new binaries

The process of creating new JDK binaries relays on the current build process of OpenJDK, therefore you can find the complete instruction [here](https://github.com/AdoptOpenJDK/openjdk-jdk11/blob/master/doc/building.md).

Below you can find the essential steps required for producing the binaries for Mac, Windows, Linux/Fedora and Linux/Debian.


### Build JDK on Mac

Verify you have installed Xcode (xcodebuild -version). We currently use Xcode 10.1 (Build version 10B61).

Download and decompress the current OpenJDK distribution (can be version 10 or 11):

    curl -O https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_osx-x64_bin.tar.gz

    tar xvf openjdk-11.0.2_osx-x64_bin.tar.gz

Clone the source code of OpenJDK 11:

    git clone https://github.com/AdoptOpenJDK/openjdk-jdk11.git

From openjdk-jdk11 root directory, execute the commands:

    bash configure --with-boot-jdk=$(pwd)/../jdk-11.0.2.jdk/Contents/Home --with-version-pre=NextFractal --with-vendor-name=NextBreakpoint --with-version-string=11.0.2+0 --with-vendor-url=https://nextbreakpoint.com

    make images

The new JDK will be create in build/macosx-x86_64-normal-server-release/images/jdk.

The static libjli will be created at build/macosx-x86_64-normal-server-release/support/native/java.base/libjli_static.a.


### Build JDK on Windows

Verify you have installed [Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/use-command-line-parameters-to-install-visual-studio?view=vs-2017) with C/C++ and UCRT.

Install Cygwin 2.5.x. Download setup tool from https://www.cygwin.com/:

Install the build tools with command:

    /cygdrive/c/Users/Administrator/Downloads/setup-x86_64 -q -P autoconf -P make -P unzip -P zip -P git -P wget -P tar   

Download and decompress the current OpenJDK distribution (can be version 10 or 11):

    wget https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_windows-x64_bin.zip

    unzip openjdk-11.0.2_windows-x64_bin.zip

Clone the source code of OpenJDK 11:

    git clone https://github.com/AdoptOpenJDK/openjdk-jdk11.git

From openjdk-jdk11 root directory, execute the commands:

    bash configure --with-boot-jdk=$(pwd)/../jdk-11.0.2 --disable-warnings-as-errors --with-version-pre=NextFractal --with-vendor-name=NextBreakpoint --with-version-string=11.0.2+0 --with-vendor-url=https://nextbreakpoint.com

    make images

The new JDK will be create in build/windows-x86_64-normal-server-release/images/jdk.


### Build JDK on Fedora

Install the build tools and libraries with commands:

    sudo yum groupinstall -y "Development Tools"
    sudo yum install -y autoconf gcc-c++ libXtst-devel libXt-devel libXrender-devel libXi-devel cups-devel fontconfig-devel alsa-lib-devel

Download and decompress the current OpenJDK distribution (can be version 10 or 11):

    wget https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_linux-x64_bin.tar.gz

    tar xvf openjdk-11.0.2_linux-x64_bin.tar.gz

Clone the source code of OpenJDK 11:

    git clone https://github.com/AdoptOpenJDK/openjdk-jdk11.git

From openjdk-jdk11 root directory, execute the commands:

    bash configure --with-boot-jdk=$(pwd)/../jdk-11.0.2 --disable-warnings-as-errors --with-version-pre=NextFractal --with-vendor-name=NextBreakpoint --with-version-string=11.0.2+0 --with-vendor-url=https://nextbreakpoint.com

    make images

The new JDK will be create in build/linux-x86_64-normal-server-release/images/jdk.


### Build JDK on Debian

Install the build tools and libraries with commands:

    sudo apt-get install -y build-essential autoconf zip libx11-dev libxext-dev libxrender-dev libxtst-dev libxt-dev libcups2-dev libfontconfig1-dev libasound2-dev

Download and decompress the current OpenJDK distribution (can be version 10 or 11):

    wget https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_linux-x64_bin.tar.gz

    tar xvf openjdk-11.0.2_linux-x64_bin.tar.gz

Clone the source code of OpenJDK 11:

    git clone https://github.com/AdoptOpenJDK/openjdk-jdk11.git

From openjdk-jdk11 root directory, execute the commands:

    bash configure --with-boot-jdk=$(pwd)/../jdk-11.0.2 --disable-warnings-as-errors --with-version-pre=NextFractal --with-vendor-name=NextBreakpoint --with-version-string=11.0.2+0 --with-vendor-url=https://nextbreakpoint.com

    make images

The new JDK will be create in build/linux-x86_64-normal-server-release/images/jdk.


## Package JDK for NextFractal

NextFractal requires Java JDK. The Java JDK image should contain only the minimal subset of the standard JDK required to run the application.

The custom JDK can be created using jlink tool available since Java 11. The JDK image can be used as embedded runtime for executing NextFractal.


### Produce JDK image for Mac

Define a variable like:

    export JDK_ROOT=$(pwd)/openjdk-jdk11/build/macosx-x86_64-normal-server-release/images/jdk

Generate the JDK image with command:

    $JDK_ROOT/bin/jlink --add-modules java.base,java.sql,java.desktop,java.compiler,java.logging,jdk.compiler,jdk.zipfs,jdk.unsupported --output nextfractal-jdk-11.0.2-0 --strip-debug --compress 2 --no-man-pages

Compress the image into an archive:

    tar -cj -f osx-nextfractal-jdk-11.0.2-0.tar.bz2 nextfractal-jdk-11.0.2-0
    zip -r9 osx-nextfractal-jdk-11.0.2-0.zip nextfractal-jdk-11.0.2-0

Verify content of archive:

    tar -tf osx-nextfractal-jdk-11.0.2-0.tar.bz2
    unzip -t osx-nextfractal-jdk-11.0.2-0.zip

Archive libjli_static:

    cp $(pwd)/openjdk-jdk11/build/macosx-x86_64-normal-server-release/support/native/java.base/libjli_static.a .
    tar -cj -f osx-libjli-static-11.0.2-0.tar.bz2 libjli_static.a
    zip -r9 osx-libjli-static-11.0.2-0.zip libjli_static.a


### Produce JDK image for Fedora

Define a variable like:

    export JDK_ROOT=$(pwd)/openjdk-jdk11/build/linux-x86_64-normal-server-release/images/jdk

Generate the JDK image with command:

    $JDK_ROOT/bin/jlink --add-modules java.base,java.sql,java.desktop,java.compiler,java.logging,jdk.compiler,jdk.zipfs,jdk.unsupported --output nextfractal-jdk-11.0.2-0 --strip-debug --compress 2 --no-man-pages

Compress the image into an archive:

    tar -cj -f fedora-nextfractal-jdk-11.0.2-0.tar.bz2 nextfractal-jdk-11.0.2-0
    zip -r9 fedora-nextfractal-jdk-11.0.2-0.zip nextfractal-jdk-11.0.2-0

Verify content of archive:

    tar -tf fedora-nextfractal-jdk-11.0.2-0.tar.bz2
    unzip -t fedora-nextfractal-jdk-11.0.2-0.zip


### Produce JDK image for Debian

Define a variable like:

    export JDK_ROOT=$(pwd)/openjdk-jdk11/build/linux-x86_64-normal-server-release/images/jdk

Generate the JDK image with command:

    $JDK_ROOT/bin/jlink --add-modules java.base,java.sql,java.desktop,java.compiler,java.logging,jdk.compiler,jdk.zipfs,jdk.unsupported --output nextfractal-jdk-11.0.2-0 --strip-debug --compress 2 --no-man-pages

Compress the image into an archive:

    tar -cj -f debian-nextfractal-jdk-11.0.2-0.tar.bz2 nextfractal-jdk-11.0.2-0
    zip -r9 debian-nextfractal-jdk-11.0.2-0.zip nextfractal-jdk-11.0.2-0

Verify content of archive:

    tar -tf debian-nextfractal-jdk-11.0.2-0.tar.bz2
    unzip -t debian-nextfractal-jdk-11.0.2-0.zip


### Produce JDK image for Windows

Define a variable like:

    export JDK_ROOT=$(pwd)/openjdk-jdk11/build/windows-x86_64-normal-server-release/images/jdk

Generate the JDK image with command:

    $JDK_ROOT/bin/jlink --add-modules java.base,java.sql,java.desktop,java.compiler,java.logging,jdk.compiler,jdk.zipfs,jdk.unsupported --output nextfractal-jdk-11.0.2-0 --strip-debug --compress 2 --no-man-pages

Compress the image into an archive:

    tar -cj -f windows-nextfractal-jdk-11.0.2-0.tar.bz2 nextfractal-jdk-11.0.2-0
    zip -r9 windows-nextfractal-jdk-11.0.2-0.zip nextfractal-jdk-11.0.2-0

Verify content of archive:

    tar -tf windows-nextfractal-jdk-11.0.2-0.tar.bz2
    unzip -t windows-nextfractal-jdk-11.0.2-0.zip
