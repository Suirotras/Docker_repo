# syntax=docker/dockerfile:1
FROM ubuntu:jammy-20230804

### update, upgrade and install some build dependencies
RUN apt-get update && apt-get upgrade -y && apt-get install -y build-essential zlib1g zlib1g-dev libcurl4-openssl-dev wget curl libpng-dev libfontconfig1-dev libxml2-dev libssl-dev libharfbuzz-dev libfribidi-dev libfreetype6-dev libpng-dev libtiff5-dev libjpeg-dev

## set local time to Europe/Stockholm
RUN CONTAINER_TIMEZONE="Europe/Stockholm" && \
    # Set timezone:
    ln -snf /usr/share/zoneinfo/$CONTAINER_TIMEZONE /etc/localtime && echo $CONTAINER_TIMEZONE > /etc/timezone

### Set LOCALE
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y locales \
    && sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen \
    && dpkg-reconfigure --frontend=noninteractive locales \
    && update-locale LANG=en_US.UTF-8
ENV LANG en_US.UTF-8 
ENV LC_ALL en_US.UTF-8

### Install R
# update indices
RUN apt-get update -qq && \
    # install two helper packages we need
    apt-get install -y --no-install-recommends software-properties-common dirmngr && \
    # add the signing key (by Michael Rutter) for these repos
    # To verify key, run gpg --show-keys /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc 
    # Fingerprint: E298A3A825C0D65DFD57CBB651716619E084DAB9
    wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc && \
    # add the R 4.0 repo from CRAN -- adjust 'focal' to 'groovy' or 'bionic' as needed
    add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/" && \
    # install R
    apt-get install -y --no-install-recommends r-base r-base-dev

### add R repo
RUN apt-get update -qq && add-apt-repository ppa:c2d4u.team/c2d4u4.0+

### create a directory for the R packages
RUN mkdir /usr/lib/container_r_packages
ENV R_LIBS=/usr/lib/container_r_packages

### add R packages
RUN R -e "install.packages(c('devtools', 'tidyverse', 'magrittr', 'optparse', 'ape', 'foreach', 'jsonlite', 'doParallel'))"

### install python
RUN apt-get update && \
    apt-get install -y python3.11

### get pip
RUN wget -qO- https://bootstrap.pypa.io/get-pip.py | python3

### install python packages
RUN pip install -U pip && \
    pip install -U pandas requests beautifulsoup4 urllib3 biopython pyfastx argparse session_info

### install gnu parallel and the java JRE
RUN apt-get update && \
    apt-get install -y parallel default-jre

### install GNU awk
RUN apt-get install -y gawk ssh

### install biomaRT
ENV R_LIBS=/usr/lib/container_r_packages
RUN R -e "install.packages('BiocManager'); BiocManager::install('biomaRt'); BiocManager::install('ggtree')"

ENTRYPOINT ["tail", "-f", "/dev/null"]

