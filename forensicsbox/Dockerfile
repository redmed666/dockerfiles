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
  gcc-multilib \
  g++-multilib \
  python3-pip \
  libzip-dev \
  tmux

RUN unlink /usr/bin/volatility && \
  rm -rf /usr/share/volatility

# Retrieve remaining dependencies
RUN cd /tmp && \
  git clone https://github.com/volatilityfoundation/volatility3.git volatility && \
  git clone https://github.com/radareorg/radare2.git radare2 && \
  git clone https://github.com/qilingframework/qiling.git qiling

RUN pip3 install yara-python

RUN cd /tmp && \
  cd radare2 && \
  ./sys/user.sh

RUN mv /tmp/volatility /usr/share/volatility && \ 
  cd /usr/share/volatility && \
  chmod +x vol.py && \
  ln -fs /usr/share/volatility/vol.py /usr/bin/

RUN cd /usr/share/volatility && \
  python3 setup.py install

RUN cd /tmp && \
  curl https://downloads.volatilityfoundation.org/volatility3/symbols/windows.zip --output windows.zip && \
  unzip windows.zip && \
  mv windows /usr/share/volatility/volatility/symbols


RUN cd /tmp && \
  cd qiling && \
  pip3 install -r requirements.txt && \
  python3 setup.py install

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
COPY radare2rc /root/.radare2rc

RUN mkdir /root/mem

ENV HOME /root
ENV USER root
WORKDIR /root/mem
