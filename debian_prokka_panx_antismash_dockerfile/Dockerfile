﻿#################################################################
# Dockerfile
#
# Version:          1.0
# Software:         prokka,panX&antismash
# Software Version: 1.12,1.0,3.0.5
# Description:      BGDMdocker: an workflow base on Docker for analysis and visualization pan-genome and biosynthetic gene clusters of Bacterial
# Code:             https://github.com/cgwyx/debian_prokka_panx_antismash_docker
# Base Image:       debian:jessie
# Build Cmd:        sudo docker build -t BGDMdocker:latest .
# Pull Cmd:         docker pull BGDMdocker:latest
# Run Cmd:          sudo docker run -it --rm -v /home:/home -p 8000:8000 --name=BGDMdocker BGDMdocker:latest
# File Author / Maintainer: cheng gong <512543469@qq.com>
#################################################################

# Set the base image to debian
FROM debian:jessie

MAINTAINER cheng gong <512543469@qq.com>

# Update the repository sources list
RUN apt-get clean all && \
    apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y \
    build-essential \
    gcc-multilib \
    apt-utils \
    expat \
    libexpat-dev \
    wget \
    git \
    dpkg \
    unzip \
    ###panx### 
    python2.7 \
    python-pip \
    python-biopython \
    python-numpy \
    python-scipy \
    ncbi-blast+ \
    mcl \
    mafft \
    fasttree \
    raxml \
    libc6 \
    libstdc++6 \
    libgcc1 \
    libtbb-dev \
    libcmph-dev \
    mpic++ && \
    ###panx finsh###
    ###prokka packages###
    libdatetime-perl \ 
    libxml-simple-perl \ 
    libdigest-md5-perl \ 
    bioperl && \
    ###prokka packages finsh###
    ###antismash packages###
    #ncbi-blast+ && \ 
		glimmerhmm && \ 
		tigr-glimmer && \ 
		diamond && \ 
		hmmer2 && \ 
		hmmer && \ 
		hmmer2-compat && \ 
		muscle && \ 
		prodigal && \ 
		fasttree && \ 
		default-jre-headless && \ 
		python-straight.plugin && \ 
		python-pysvg && \ 
		python-helperlibs && \ 
		#python-biopython && \ 
		python-pyquery && \ 
		python-backports.lzma && \ 
		python-matplotlib && \
    ###antismash packages finsh###
    apt-get clean && \
    apt-get purge && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

###prokka installation ###    
# Java Version
ENV  JAVA_VERSION=8 \
     JAVA_UPDATE=111 \
     JAVA_BUILD=14  \
     JAVA_HOME=/usr/lib/jvm/current-java

# JDK: http://download.oracle.com/otn-pub/java/jdk/8u60-b27/jdk-8u60-linux-x64.tar.gz
# JRE: http://download.oracle.com/otn-pub/java/jdk/8u60-b27/jre-8u60-linux-x64.tar.gz
# http://download.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.tar.gz
# http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-x64.tar.gz

# JRE
RUN cd /tmp && wget --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
        "http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD}/jre-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" && \
     tar xzf "jre-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" && \
     mkdir -p /usr/lib/jvm && mv "/tmp/jre1.${JAVA_VERSION}.0_${JAVA_UPDATE}" "/usr/lib/jvm/java-${JAVA_VERSION}-oracle"  && \
     ln -s "java-${JAVA_VERSION}-oracle" $JAVA_HOME && \
     ln -s $JAVA_HOME/bin/java /usr/bin/java && \
     rm -rf $JAVA_HOME/*.txt && \
     rm -rf $JAVA_HOME/*.html
     #apk del wget ca-certificates curl && \
     #rm /tmp/* /var/cache/apk/*

#instation prokka#
RUN git clone https://github.com/tseemann/prokka.git && \
    wget ftp://ftp.ncbi.nih.gov/toolbox/ncbi_tools/converters/by_program/tbl2asn/linux64.tbl2asn.gz && \
    gunzip linux64.tbl2asn.gz && \
    cp linux64.tbl2asn /prokka/binaries/linux/tbl2asn && \
    chmod a+x /prokka/binaries/linux/tbl2asn && \
    /prokka/bin/prokka --setupdb
    
ENV PATH /prokka/bin:$PATH
ENV PATH /prokka/binaries/linux:$PATH
ENV PATH /prokka/binaries/common:$PATH
##prokka installation finsh ##
  
###panx installation###
RUN pip install ete2 --timeout 6000

RUN git clone https://github.com/neherlab/treetime.git && \
    cd treetime && \
    python setup.py install

RUN wget https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/orthagogue/orthAgogue-1.0.2.deb && \
    dpkg -i orthAgogue-1.0.2.deb && \
    apt-get clean
    
#RUN git clone https://github.com/neherlab/pan-genome-analysis.git
RUN wget -t 0 https://github.com/neherlab/pan-genome-analysis/archive/handle_premature_stop_codon.zip && \
    unzip handle_premature_stop_codon.zip && \
    mv pan-genome-analysis-handle_premature_stop_codon pan-genome-analysis && \
    chmod -R 700 pan-genome-analysis/tools/diamond

RUN wget -qO- https://deb.nodesource.com/setup_4.x | bash - && \
    apt-get install nodejs && \
    apt-get clean
    
RUN git clone https://github.com/neherlab/pan-genome-visualization.git && \
    cd pan-genome-visualization && \
    npm install

RUN ln -s /usr/bin/raxmlHPC /usr/bin/raxml

ENV PATH /pan-genome-analysis/tools:$PATH

#Expose port 8000 (webserver)
EXPOSE :8000
##panx installation finsh##

###antiSMASH installation###
# set up antiSMASH deb repo
ADD http://dl.secondarymetabolites.org/antismash.list /etc/apt/sources.list.d/antismash.list
ADD http://dl.secondarymetabolites.org/antismash.asc /tmp/
RUN apt-key add /tmp/antismash.asc

ENV ANTISMASH_URL="https://bitbucket.org/antismash/antismash/downloads"
ENV ANTISMASH_VERSION="3.0.5"

# Grab antiSMASH
ADD ${ANTISMASH_URL}/antismash-${ANTISMASH_VERSION}.tar.gz /home/
RUN tar xf /home/antismash-${ANTISMASH_VERSION}.tar.gz

# Grab the databases
WORKDIR antismash-${ANTISMASH_VERSION}
RUN python download_databases.py

ADD instance.cfg antismash/config/instance.cfg

WORKDIR /usr/local/bin
RUN ln -s /antismash-${ANTISMASH_VERSION}/run_antismash.py

WORKDIR /antismash-3.0.5/antismash/
##antiSMASH installation finsh##


CMD ["/bin/bash"]


