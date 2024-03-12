
FROM ubuntu:22.04
LABEL maintainer="Gerrit Botha gerrit.botha@uct.ac.za"

# Followed instructions here: https://github.com/conda/conda-docker/tree/master/miniconda3/debian
RUN apt-get -qq update && apt-get -qq -y install curl bzip2 wget libjemalloc-dev \
    && apt-get -qq -y remove curl bzip2 \
    && apt-get -qq -y autoremove \
    && apt-get autoclean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log

RUN wget -q https://s3.amazonaws.com/sentieon-release/software/sentieon-genomics-202308.02.tar.gz 

RUN tar -xzvf sentieon-genomics-202308.02.tar.gz && \
    rm sentieon-genomics-202308.02.tar.gz

RUN mv sentieon-genomics-202308.02 /opt

ldconfig

# Switch back to the ubuntu user so this tool (and the files written) are not owned by root
RUN groupadd -r -g 1000 ubuntu && useradd -r -g ubuntu -u 1000 -m ubuntu
USER ubuntu

ENV PATH /opt/sentieon-genomics-202308.02/bin/:$PATH
