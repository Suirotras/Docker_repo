# syntax=docker/dockerfile:1
FROM ubuntu:jammy-20240111

### update, upgrade and install some build dependencies
RUN apt-get update && apt-get upgrade -y && apt-get install -y build-essential wget

### set local time to Europe/Stockholm
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

###  Install texlive
RUN apt-get update && apt-get install -y texlive-full

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
    apt-get install -y --no-install-recommends r-base

### add R repo
RUN apt-get update -qq && add-apt-repository ppa:c2d4u.team/c2d4u4.0+

### Get the texshade package
RUN cd /usr/local/share/texmf/ && \
    mkdir -p tex/latex/ && \
    cd tex/latex/ && \
    wget https://mirrors.ctan.org/macros/latex/contrib/texshade.zip && \
    unzip texshade.zip && \
    rm texshade.zip && \
    cd texshade && \
    # get neccesary files
    latex texshade.ins && \
    # fix bug in texshade.sty.
    # to fix bugs in texshade.sty
    sed -i 's=\\xdef\\par@{\\string\\par}=\\xdef\\par@{\\par}\n\\xdef\\par@{\\expandafter\\string\\par@}=g' texshade.sty && \
    sed -i 's=\\def\\get@tintregions#1..#2,#3@{%=\\def\\get@{\\par}\n\\def\\get@tintregions#1..#2,#3@{%=g' texshade.sty && \
    # get documentation (run twice)
    latex -output-format=pdf texshade.dtx - && \
    latex -output-format=pdf texshade.dtx && \
    cd /usr/local/share/texmf/ && \
    # update tex database
    mktexlsr

# get additional R packages
RUN apt-get update && \
    apt-get install -y zlib1g zlib1g-dev curl && \
    apt-get install -y r-cran-tidyverse r-cran-magrittr r-cran-rcurl && \
    R -e "install.packages('BiocManager'); BiocManager::install(c('Biostrings', 'msa'))"

ENTRYPOINT ["tail", "-f", "/dev/null"]
