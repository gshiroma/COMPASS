##### General instruction: ######
#docker build -f ./Dockerfile -t opera-adt/compass:latest . #To build the docker image
#to run docker container in interactive mode: docker run -it --name COMPASS opera-adt/compass:latest /bin/bash

FROM ubuntu:22.04

LABEL author="OPERA ADT" \
      description="s1 cslc 0.1.0 IF release" \
      version="0.1.0_IF"

RUN apt-get -y update &&\
    apt-get -y install curl git &&\
    adduser --disabled-password compass_user

USER compass_user 

COPY docker/specifile.txt /home/compass_user/

ENV CONDA_PREFIX=/home/compass_user/miniconda3

WORKDIR /home/compass_user
RUN curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o miniconda.sh &&\
    bash miniconda.sh -b -p ${CONDA_PREFIX} &&\
    rm $HOME/miniconda.sh

ENV PATH="${CONDA_PREFIX}/bin:${PATH}"
RUN $CONDA_PREFIX/bin/conda init bash

RUN conda create --name "COMPASS" --file specifile.txt

RUN echo "Installing OPERA s1-reader" &&\
    mkdir -p $HOME/OPERA &&\
    cd $HOME/OPERA &&\
    curl -sSL https://github.com/opera-adt/s1-reader/archive/refs/tags/v0.1.0.tar.gz -o s1_reader_src.tar.gz &&\
    tar -xvf s1_reader_src.tar.gz &&\
    ln -s s1-reader-0.1.0 s1-reader &&\
    rm s1_reader_src.tar.gz &&\
    python -m pip install ./s1-reader

SHELL ["conda", "run", "-n", "COMPASS", "/bin/bash", "-c"]

COPY . /home/compass_user/OPERA/COMPASS

USER root
RUN chown -R compass_user:compass_user /home/compass_user/OPERA/COMPASS
USER compass_user

RUN echo "Installing OPERA COMPASS" &&\
    cd $HOME/OPERA &&\
    python -m pip install ./COMPASS

WORKDIR /home/compass_user/scratch

#for COMPASS 0.1.1
ENTRYPOINT ["conda", "run", "-n", "COMPASS","s1_cslc.py"]
