ARG HTTP_PROXY=""
ARG HTTPS_PROXY=""
FROM kalilinux/kali-linux-docker:latest

ENV http_proxy=$HTTP_PROXY
ENV https_proxy=$HTTPS_PROXY

RUN apt update && \
  apt upgrade -y

RUN apt install -y \
  kali-linux-forensics

RUN apt install -y \
  apt-utils \
  automake \
  build-essential \
  sudo \
  git \
  peframe \
  foremost \
  binutils \
  scalpel \
  ipython \
  libbz2-dev \
  libc6-dev \
  libfreetype6-dev \
  libgdbm-dev \
  libjansson-dev \
  libmagic-dev \
  libreadline-gplv2-dev \
  libtool \
  python2.7 \
  python-dev \
  python-pillow \
  python-pip \
  tar \
  unzip \
  wget \
  zlib1g \
  zlib1g-dev \
  zsh \
  screen \
  curl \
  vim \
  pkg-config \
  cmake \
  bison \
  flex \
  tmux

RUN unlink /usr/bin/volatility && \
  rm -rf /usr/share/volatility

# Retrieve remaining dependencies
RUN cd /tmp && \
  wget -O yara-v3.4.0.tar.gz "https://github.com/plusvic/yara/archive/v3.4.0.tar.gz" && \
  git clone https://github.com/volatilityfoundation/volatility.git volatility && \
  git clone https://github.com/radareorg/radare2.git radare2

RUN  cd /tmp && \
  tar vxzf yara-v3.4.0.tar.gz && \
  cd yara-3.4.0 && \
  ./bootstrap.sh && \
  ./configure && \
  make && \
  make install && \
  cd yara-python/ && \
  python setup.py build && \
  python setup.py install

RUN cd /tmp && \
  cd radare2 && \
  ./sys/user.sh

RUN mv /tmp/volatility /usr/share/volatility && \ 
  cd /usr/share/volatility && \
  chmod +x vol.py && \
  ln -fs /usr/share/volatility/vol.py /usr/bin/

ENV PATH /root/bin:$PATH

RUN pip3 install frida frida-tools r2pipe

RUN cd /tmp && \
    git clone https://github.com/longld/peda.git peda &&                                  \
    echo "source /tmp/peda/peda.py" >> /root/.gdbinit

RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash && \
    export NVM_DIR="$HOME/.nvm" &&                                                          \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  &&                                    \
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" &&                   \
    nvm install 10.5.0 && \
    nvm use node

ENV PATH=/root/bin:/root/.nvm/versions/node/v10.5.0/bin:$PATH

RUN r2pm init &&                                                                            \
    r2pm update &&                                                                          \
    r2pm -i r2msdn &&                                                                       \
    r2pm -i r2dec &&									    \
    r2pm -i r2ghidra-dec &&								    \
    r2pm -i r2frida

RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

RUN chsh -s $(which zsh)

COPY tmux.conf /root/.tmux.conf
COPY vimrc /root/.vimrc

ENV HOME /root
ENV USER root
WORKDIR /root/
