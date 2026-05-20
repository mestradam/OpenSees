FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    make \
    tcl8.6 \
    tcl8.6-dev \
    gcc \
    g++ \
    gfortran \
    hdf5-dev \
    liblapack-dev \
    libblas-dev \
    libarpack2-dev \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /home/user/bin /home/user/lib

RUN git clone https://github.com/mestradam/OpenSees.git /home/user/OpenSees

WORKDIR /home/user/OpenSees

RUN make

RUN useradd -m user && chown -R user:user /home/user

USER user
ENV HOME=/home/user
ENV PATH=$HOME/bin:$PATH

CMD ["tclsh8.6", "/home/user/OpenSees/SRC/tcl/OpenSees.tcl"]
