FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    git \
    make \
    tcl8.6 \
    tcl8.6-dev \
    gcc \
    g++ \
    gfortran \
    libhdf5-dev \
    liblapack-dev \
    libblas-dev \
    libarpack2-dev \
    && rm -rf /var/lib/apt/lists/*

RUN useradd -m user

RUN mkdir -p /home/user/bin /home/user/lib /home/user/OpenSees

WORKDIR /home/user/OpenSees

RUN git clone https://github.com/mestradam/OpenSees.git /home/user/OpenSees

RUN chown -R user:user /home/user

USER user

RUN make
ENV HOME=/home/user
ENV PATH=$HOME/bin:$PATH

CMD ["tclsh8.6", "/home/user/OpenSees/SRC/tcl/OpenSees.tcl"]
