# Name: Mobile-Security-Framework
# Website: https://mobsf.github.io/Mobile-Security-Framework-MobSF/
# Description: 
# Author: Ajin Abraham
# License: GNU General Public License v3.0 (https://github.com/MobSF/Mobile-Security-Framework-MobSF/blob/master/LICENSE)
# Notes: 

FROM ubuntu:20.04

# Labels and Credits
LABEL \
    name="MobSF" \
    author="Ajin Abraham <ajin25@gmail.com>" \
    maintainer="Ajin Abraham <ajin25@gmail.com>" \
    contributor_1="OscarAkaElvis <oscar.alfonso.diaz@gmail.com>" \
    contributor_2="Vincent Nadal <vincent.nadal@orange.fr>" \
    description="Mobile Security Framework (MobSF) is an automated, all-in-one mobile application (Android/iOS/Windows) pen-testing, malware analysis and security assessment framework capable of performing static and dynamic analysis."

# Environment vars
ENV DEBIAN_FRONTEND="noninteractive" \
    ANALYZER_IDENTIFIER="" 

# See https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#run
RUN apt-get update -y && apt-get install -y  --no-install-recommends \
    git \
    build-essential \
    locales \
    sqlite3 \
    fontconfig-config \
    libjpeg-turbo8 \
    libxrender1 \
    libfontconfig1 \
    libxext6 \
    fontconfig \
    xfonts-75dpi \
    xfonts-base \
    python3.8 \
    python3-dev \
    python3-pip \
    wget \
    android-tools-adb

# Set locales
RUN locale-gen en_US.UTF-8
ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

# Install Wkhtmltopdf
RUN wget --quiet -O /tmp/wkhtmltox_0.12.5-1.focal_amd64.deb "https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.focal_amd64.deb" && \
    dpkg -i /tmp/wkhtmltox_0.12.5-1.focal_amd64.deb && \
    apt-get install -f -y --no-install-recommends && \
    ln -s /usr/local/bin/wkhtmltopdf /usr/bin && \
    rm -rf /tmp/wkhtmltox_0.12.5-1.focal_amd64.deb

# Install OpenJDK12
WORKDIR /root/
RUN wget --quiet https://download.java.net/java/GA/jdk12/GPL/openjdk-12_linux-x64_bin.tar.gz && \
    tar zxf openjdk-12_linux-x64_bin.tar.gz && \
    rm -rf openjdk-12_linux-x64_bin.tar.gz
ENV JAVA_HOME="/jdk-12"
ENV PATH="$JAVA_HOME/bin:$PATH"

RUN git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF 
#/root/Mobile-Security-Framework-MobSF
WORKDIR /root/Mobile-Security-Framework-MobSF
RUN python3 -m pip install --quiet --no-cache-dir -r /root/Mobile-Security-Framework-MobSF/requirements.txt
# Install python3 requirements
#RUN python3 -m pip install --quiet --no-cache-dir Django==3.1.2 pdfkit==0.6.1 androguard==3.3.5 lxml==4.5.2 rsa==4.6 biplist==1.0.3 \
#    requests==2.24.0 bs4==0.0.1 colorlog==4.2.1 macholib==1.14 google-play-scraper==0.1.1 whitenoise==5.2.0 \
#    waitress==1.4.4 gunicorn==20.0.4 frida==12.11.17 psutil==5.6.2 shelljob==0.6.2 asn1crypto==1.4.0 \
#    oscrypto==1.2.1 distro==1.5.0 IP2Location==8.4.1 lief==0.10.1 http-tools==1.1.1 libsast==1.3.0 apkid==2.1.0 \
#    cryptography<3.0,>=2.9 pyOpenSSL<19.2,>=19.1.0

RUN python3 -m pip install scripts/wheels/yara_python-3.11.0-cp38-cp38-linux_x86_64.whl && \
    rm -rf scripts/wheels > /dev/null 2>&1

# Cleanup
RUN \
    apt remove -y \
        libssl-dev \
        libffi-dev \
        libxml2-dev \
        libxslt1-dev \
        python3-dev \
        wget && \
    apt clean && \
    apt autoclean && \
    apt autoremove -y && \
    rm -rf /var/lib/apt/lists/* /tmp/* > /dev/null 2>&1

# Enable Use Home Directory and set adb path
RUN sed -i 's/USE_HOME = False/USE_HOME = True/g' MobSF/settings.py && \
    sed -i "s#ADB_BINARY = ''#ADB_BINARY = '/usr/bin/adb'#" MobSF/settings.py

# Postgres support is set to false by default
ARG POSTGRES=False

ENV POSTGRES_USER=postgres
ENV POSTGRES_PASSWORD=password
ENV POSTGRES_DB=mobsf
ENV POSTGRES_HOST=postgres

# Check if Postgres support needs to be enabled
WORKDIR /root/Mobile-Security-Framework-MobSF/scripts
RUN chmod +x postgres_support.sh; sync; ./postgres_support.sh $POSTGRES
WORKDIR /root/Mobile-Security-Framework-MobSF

# Add apktool working path
RUN mkdir -p /root/.local/share/apktool/framework

# Expose MobSF Port
EXPOSE 8000
# MobSF Proxy
EXPOSE 1337

RUN chmod 755 /root/Mobile-Security-Framework-MobSF/scripts/entrypoint.sh

# Run MobSF
CMD ["/root/Mobile-Security-Framework-MobSF/scripts/entrypoint.sh"]
