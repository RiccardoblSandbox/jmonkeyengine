FROM azul/zulu-openjdk:8

#Based on https://github.com/mreichelt/docker-android LABEL maintainer="mcreichelt@gmail.com"

ENV ANDROID_SDK_URL="https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip" \
    ANDROID_HOME="/opt/android" \
    ANDROID_NDK_HOME="/opt/android/ndk-bundle"

ENV PATH $PATH:$ANDROID_HOME/tools:$ANDROID_HOME/tools/bin:$ANDROID_HOME/platform-tools:$ANDROID_HOME/build-tools/$ANDROID_BUILD_TOOLS_VERSION:${ANDROID_NDK_HOME}

WORKDIR /opt

# Install some dependencies
RUN  apt-get update  && \
    apt-get install -y curl git unzip zip build-essential 

# Download emulator script
ADD android-wait-for-emulator /opt/android-wait-for-emulator
RUN chmod +x android-wait-for-emulator

# Install Android SDK tools
RUN mkdir android && cd android && \
    curl -L ${ANDROID_SDK_URL} -o tools.zip && \
    unzip -qq tools.zip && \
    rm tools.zip

RUN yes | sdkmanager 'tools' 'platform-tools' 

RUN export LD_LIBRARY_PATH=$ANDROID_HOME/emulator/lib64/qt/lib/


# get more from `sdkmanager --list` (add '--verbose' to read long package names)
RUN yes | sdkmanager  'build-tools;29.0.0' 'cmake;3.6.4111459' 'lldb;3.1'

RUN yes | sdkmanager ndk-bundle 

RUN curl https://downloads.gradle.org/distributions/gradle-5.5-bin.zip -o /tmp/gradle.zip
RUN if [ "`sha256sum /tmp/gradle.zip | cut -d' ' -f1`" != "8d78b2ed63e7f07ad169c1186d119761c4773e681f332cfe1901045b1b0141bc" ];\
    then \
        echo "Error. This version of gradle is corrupted."; \
        exit 1;\
    fi && \
    mkdir -p /tmp/gradle && \
    unzip -q -d /tmp/gradle /tmp/gradle.zip &&\
    cp -Rf /tmp/gradle/gradle-*/* / &&\
    rm -Rf /tmp/gradle && rm -f /tmp/gradle.zip && \
    echo "Installed gradle `gradle -v`"



ENV ANDROID_NDK_HOME ${ANDROID_HOME}/ndk-bundle
ENV ANDROID_NDK $ANDROID_HOME/ndk-bundle/

