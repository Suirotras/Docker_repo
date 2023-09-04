# syntax=docker/dockerfile:1
FROM ubuntu:jammy-20230804

### update, upgrade and install some build dependencies
RUN apt-get update && apt-get upgrade -y && apt-get install -y build-essential zlib1g zlib1g-dev libcurl4-openssl-dev wget curl

## set local time to Europe/Stockholm
RUN CONTAINER_TIMEZONE="Europe/Stockholm" && \
    # Set timezone:
    ln -snf /usr/share/zoneinfo/$CONTAINER_TIMEZONE /etc/localtime && echo $CONTAINER_TIMEZONE > /etc/timezone

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
    apt-get install -y --no-install-recommends r-base

### add R repo
RUN apt-get update -qq && add-apt-repository ppa:c2d4u.team/c2d4u4.0+

### add R packages
RUN apt-get update && \
    # get R packages required for installation of other packages
    apt-get install -y r-cran-memoise r-cran-sass r-cran-knitr r-cran-xml2 r-cran-openssl r-cran-curl r-cran-credentials r-cran-httr2 \
    r-cran-httpuv r-cran-bslib r-cran-rmarkdown r-cran-gert r-cran-gh r-cran-shiny r-cran-downlit r-cran-httr r-cran-ragg r-cran-htmlwidgets \
    r-cran-usethis r-cran-miniui r-cran-pkgdown r-cran-profvis r-cran-rcmdcheck r-cran-roxygen2 r-cran-rversions r-cran-rcurl r-cran-devtools && \
    # get R packages needed for 
    apt-get install -y r-cran-purrr r-cran-dplyr r-cran-readr r-cran-ape r-cran-modules r-cran-magrittr && \
    R -e "install.packages('BiocManager'); BiocManager::install(c('XVector', 'GenomeInfoDb', 'Biostrings'))"

### install python
RUN apt-get update && \
    apt-get install -y python3.11

### get pip
RUN wget -qO- https://bootstrap.pypa.io/get-pip.py | python3

### install python packages
RUN pip install -U pip && \
    pip install -U pandas biopython pyfastx argparse session_info

### install HyPhy dependencies
RUN apt-get update && \
    apt-get install -y cmake gcc libcurl4 && \
    apt-get install -y libomp-dev openmpi-bin openmpi-common libopenmpi-dev git

### install HyPhy
RUN git clone https://github.com/veg/hyphy.git && \
    cd hyphy && \
    cmake . && \
    make HYPHYMPI && \
    make install

ENTRYPOINT ["tail", "-f", "/dev/null"]