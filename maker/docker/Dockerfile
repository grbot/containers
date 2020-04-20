
FROM ubuntu:18.04
MAINTAINER Gerrit Botha "gerrit.botha@uct.ac.za"

ARG DEBIAN_FRONTEND=noninteractive

# Followed instructions here: https://github.com/conda/conda-docker/tree/master/miniconda3/debian
RUN apt-get -qq update && apt-get -qq -y install libglib2.0-dev wget unzip zip libfindbin-libs-perl build-essential automake vim gcc make libexpat1-dev postgresql postgresql-server-dev-all pkg-config cpio snap \
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
COPY XML-DOM-XPath-0.14.tar.gz /
RUN tar -xzvf XML-DOM-XPath-0.14.tar.gz
RUN cd /XML-DOM-XPath-0.14 && \
perl Makefile.PL && \
make && \
make test && \
make install

RUN perl -MCPAN -e 'CPAN::install(Bio::Root::Version)'

# Install SNAP / installed from package
#RUN wget http://korflab.ucdavis.edu/Software/snap-2013-11-29.tar.gz
#RUN tar -xzvf snap-2013-11-29.tar.gz
RUN ln /usr/bin/snap-hmm /usr/bin/snap

# Install Blast
RUN wget https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/ncbi-blast-2.10.0+-src.tar.gz
RUN tar -xzvf ncbi-blast-2.10.0+-src.tar.gz
RUN cd /ncbi-blast-2.10.0+-src/c++ && \
./configure && \
make && \
make install
ENV PATH $PATH:/ncbi-blast-2.10.0+-src/c++

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
RUN wget http://eddylab.org/software/hmmer/hmmer.tar.gz
RUN tar -xzvf hmmer.tar.gz
RUN cd hmmer-3.3 && \
./configure && \
make && \
make install

# Install RepeatMasker
RUN wget http://www.repeatmasker.org/RepeatMasker-4.1.0.tar.gz
RUN tar -xzvf RepeatMasker-4.1.0.tar.gz
# Installed the following manually and then did a commit to an image
#RUN cd RepeatMasker && \
#perl ./configure
#ENV PATH $PATH:/RepeatMasker

COPY maker-3.01.02-beta.tgz /
RUN tar -xzvf maker-3.01.02-beta.tgz
RUN cd /maker/src/ && \
perl Build.PL && \
./Build install
ENV PATH $PATH:/maker/bin

# Switch back to the ubuntu user so this tool (and the files written) are not owned by root
RUN groupadd -r -g 1000 ubuntu && useradd -r -g ubuntu -u 1000 -m ubuntu
USER ubuntu
