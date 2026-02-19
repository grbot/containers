FROM mambaorg/micromamba:1.5.10

LABEL maintainer="Gerrit Botha <gerrit.botha@uct.ac.za>"

USER root
RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates curl procps  \
 && rm -rf /var/lib/apt/lists/*

COPY environment.yml /tmp/environment.yml

RUN micromamba create -y -n pangene -f /tmp/environment.yml \
 && micromamba clean -a -y

# pangene.js helper (README uses: k8 pangene.js ...)
RUN curl -fsSL https://raw.githubusercontent.com/lh3/pangene/master/pangene.js -o /usr/local/bin/pangene.js \
 && chmod +x /usr/local/bin/pangene.js

# Create non-root user (matches your usual convention)
RUN groupadd -r -g 1000 ubuntu && useradd -r -g ubuntu -u 1000 -m ubuntu

USER ubuntu
ENV PATH=/opt/conda/envs/pangene/bin:/usr/local/bin:$PATH

ENV PATH=/opt/conda/envs/pangene/bin:/usr/local/bin:$PATH
