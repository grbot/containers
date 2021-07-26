
FROM ubuntu:18.04
MAINTAINER Gerrit Botha "gerrit.botha@uct.ac.za"

ARG DEBIAN_FRONTEND=noninteractive

# Followed instructions here: https://github.com/conda/conda-docker/tree/master/miniconda3/debian
RUN apt-get -qq update && apt-get -qq -y install libglib2.0-dev wget unzip zip libfindbin-libs-perl build-essential automake vim gcc make libexpat1-dev postgresql postgresql-server-dev-all pkg-config cpio snap git autoconf \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log

ENV PERL_MM_USE_DEFAULT 1

# Do some Perl setup
RUN perl -MCPAN -e 'CPAN::install(DBI)'
RUN perl -MCPAN -e 'CPAN::install(DBD::SQLite)'
RUN perl -MCPAN -e 'CPAN::install(forks)'
RUN perl -MCPAN -e 'CPAN::install(forks::shared)'
RUN perl -MCPAN -e 'CPAN::install(File::Which)'
RUN perl -MCPAN -e 'CPAN::install(Perl::Unsafe::Signals)'
RUN perl -MCPAN -e 'CPAN::install(Bit::Vector)'
RUN perl -MCPAN -e 'CPAN::install(Inline::C)'
RUN perl -MCPAN -e 'CPAN::install(IO::All)'
RUN perl -MCPAN -e 'CPAN::install(IO::Prompt)'
RUN perl -MCPAN -e 'CPAN::install(Test::Pod::Coverage)'
RUN perl -MCPAN -e 'CPAN::install(XML::DOM::XPath)'
RUN perl -MCPAN -e 'CPAN::install(DBD::Pg)'

# Install custom XML-DOM-XPath
# Download tarball from: https://www.cpan.org/modules/by-module/XML/
# Edit file names depending on version available
COPY XML-DOM-XPath-0.14.tar.gz /
RUN tar -xzvf XML-DOM-XPath-0.14.tar.gz
RUN cd /XML-DOM-XPath-0.14 && \
perl Makefile.PL && \
make && \
make test && \
make install

RUN perl -MCPAN -e 'CPAN::install(Bio::Root::Version)'

# Install SNAP - already installed from package (line 8)
#RUN wget http://korflab.ucdavis.edu/Software/snap-2013-11-29.tar.gz
#RUN tar -xzvf snap-2013-11-29.tar.gz
RUN ln /usr/bin/snap-hmm /usr/bin/snap

# Install Blast
RUN wget https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/ncbi-blast-2.12.0+-src.tar.gz
RUN tar -xzvf ncbi-blast-2.12.0+-src.tar.gz
RUN cd /ncbi-blast-2.12.0+-src/c++ && \
./configure && \
make && \
make install
ENV PATH $PATH:/ncbi-blast-2.12.0+-src/c++

# Install Exonorate
ENV PATH $PATH:/root/.cpan/build/PkgConfig-0.23026-0/script/
RUN wget http://ftp.ebi.ac.uk/pub/software/vertebrategenomics/exonerate/exonerate-2.2.0-x86_64.tar.gz
RUN tar -xzvf exonerate-2.2.0-x86_64.tar.gz
ENV PATH $PATH:/exonerate-2.2.0-x86_64/bin

# Install TRF
RUN wget http://tandem.bu.edu/trf/downloads/trf409.linux64
RUN chmod +x /trf409.linux64
RUN mv trf409.linux64 /usr/local/bin/

# Install HMMER
RUN wget http://eddylab.org/software/hmmer/hmmer-3.3.2.tar.gz
RUN tar -xzvf hmmer-3.3.2.tar.gz
RUN cd hmmer-3.3.2 && \
./configure && \
make && \
make install

# Install RepeatMasker
RUN wget http://www.repeatmasker.org/RepeatMasker-4.1.0.tar.gz
RUN tar -xzvf RepeatMasker-4.1.0.tar.gz
RUN perl -MCPAN -e 'CPAN::install(Text::Soundex)'
# Installed the following manually and then committed to another image
# cd RepeatMasker
# perl ./configure
# export PATH=$PATH:/RepeatMasker

# Install EVidenceModeler
RUN wget https://github.com/EVidenceModeler/EVidenceModeler/archive/refs/tags/v1.1.1.tar.gz
RUN tar -xzvf v1.1.1.tar.gz
ENV PATH $PATH:/EVidenceModeler-1.1.1

# Install Augustus - copied from: https://github.com/Gaius-Augustus/Augustus/blob/master/Dockerfile
# Set timezone in tzdata
ENV DEBIAN_FRONTEND="noninteractive" TZ="Europe/Berlin"

# Install required packages - added these to line 8
#RUN apt-get update
#RUN apt-get install -y build-essential wget git autoconf

# Install dependencies for AUGUSTUS comparative gene prediction mode (CGP)
RUN apt-get update
RUN apt-get install -y libgsl-dev libboost-all-dev libsuitesparse-dev liblpsolve55-dev
RUN apt-get install -y libsqlite3-dev libmysql++-dev

# Install dependencies for the optional support of gzip compressed input files
RUN apt-get install -y libboost-iostreams-dev zlib1g-dev

# Install dependencies for bam2hints and filterBam 
RUN apt-get install -y libbamtools-dev

# Install additional dependencies for bam2wig
RUN apt-get install -y samtools libhts-dev

# Install additional dependencies for homGeneMapping and utrrnaseq
RUN apt-get install -y libboost-all-dev

# Install additional dependencies for scripts
RUN apt-get install -y cdbfasta diamond-aligner libfile-which-perl libparallel-forkmanager-perl libyaml-perl libdbd-mysql-perl
RUN apt-get install -y --no-install-recommends python3-biopython

# Clone AUGUSTUS repository
# Download from: https://github.com/Gaius-Augustus/Augustus
# Unzip in build context: tar -xzvf augustus.tar.gz
ADD Augustus /root/augustus

# Build AUGUSTUS
WORKDIR /root/augustus
RUN make && \ 
make install
ENV PATH="/root/augustus/bin:/root/augustus/scripts:${PATH}"

# Test AUGUSTUS
RUN make unit_test

# Download MAKER from: http://yandell.topaz.genetics.utah.edu/cgi-bin/maker_license.cgi
WORKDIR /
COPY maker-3.01.03.tgz /
RUN tar -xzvf maker-3.01.03.tgz
RUN cd /maker/src/ && \
perl Build.PL && \
./Build install
ENV PATH $PATH:/maker/bin

# Switch back to the ubuntu user so this tool (and the files written) are not owned by root
RUN groupadd -r -g 1000 ubuntu && useradd -r -g ubuntu -u 1000 -m ubuntu
USER ubuntu
