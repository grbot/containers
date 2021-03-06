
FROM ubuntu:18.04
MAINTAINER Gerrit Botha "gerrit.botha@uct.ac.za"

ARG DEBIAN_FRONTEND=noninteractive

# Followed instructions here: https://github.com/conda/conda-docker/tree/master/miniconda3/debian
RUN apt-get -qq update && apt-get -qq -y install curl bzip2 wget unzip openjdk-8-jre zip r-base munge \
    && curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o /tmp/miniconda.sh \
    && bash /tmp/miniconda.sh -bfp /usr/local \
    && rm -rf /tmp/miniconda.sh \
    && conda install -y python=3 \
    && conda update conda \
    && apt-get -qq -y remove curl bzip2 \
    && apt-get -qq -y autoremove \
    && apt-get autoclean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log \
    && conda clean --all --yes

ENV PATH /opt/conda/bin:$PATH

COPY environment.yml /
RUN conda env create -f /environment.yml && conda clean -a

RUN mkdir /var/run/munge
ENV DRMAA_LIBRARY_PATH /cbio/soft/slurm-drmaa/lib/libdrmaa.so.1
ENV PATH=$PATH:/cbio/soft/genome-strip/svtoolkit-2.00.1949/bwa:/cbio/soft/slurm-drmaa/bin:/opt/slurm/bin
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/cbio/soft/genome-strip/svtoolkit-2.00.1949/bwa:/cbio/soft/slurm-drmaa/lib:/opt/slurm/lib
ENV INCLUDE=$INCLUDE:/cbio/soft/slurm-drmaa/include:/opt/slurm/include


# Switch back to the ubuntu user so this tool (and the files written) are not owned by root
RUN groupadd -r -g 1000 ubuntu && useradd -r -g ubuntu -u 1000 -m ubuntu
USER ubuntu

ENV PATH /usr/local/envs/genomestrip/bin/:$PATH
