
FROM ubuntu:18.04
MAINTAINER Gerrit Botha "gerrit.botha@uct.ac.za"

# Followed instructions here: https://github.com/conda/conda-docker/tree/master/miniconda3/debian
RUN apt-get -qq update && apt-get -qq -y install curl bzip2 wget unzip zip libfindbin-libs-perl \
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

RUN wget -q https://sourceforge.net/projects/deconseq/files/standalone/deconseq-standalone-0.4.3.tar.gz

RUN tar -xzvf deconseq-standalone-0.4.3.tar.gz && \
    rm deconseq-standalone-0.4.3.tar.gz

RUN chmod +x /deconseq-standalone-0.4.3/deconseq.pl
RUN chmod +x /deconseq-standalone-0.4.3/splitFasta.pl
RUN chmod +x /deconseq-standalone-0.4.3/bwa64

COPY environment.yml /
RUN conda env create -f /environment.yml && conda clean -a

# Switch back to the ubuntu user so this tool (and the files written) are not owned by root
RUN groupadd -r -g 1000 ubuntu && useradd -r -g ubuntu -u 1000 -m ubuntu
USER ubuntu

ENV PATH /usr/local/envs/deconseq/bin/:/deconseq-standalone-0.4.3/:$PATH
