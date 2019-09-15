FROM kalilinux/kali-linux-docker:latest

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
  tmux

RUN unlink /usr/bin/volatility && \
  rm -rf /usr/share/volatility

# Retrieve remaining dependencies
RUN cd /tmp && \
  wget -O yara-v3.4.0.tar.gz "https://github.com/plusvic/yara/archive/v3.4.0.tar.gz" && \
  git clone https://github.com/volatilityfoundation/volatility.git volatility

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

RUN mv /tmp/volatility /usr/share/volatility && \ 
  cd /usr/share/volatility && \
  chmod +x vol.py && \
  ln -fs /usr/share/volatility/vol.py /usr/bin/

RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

RUN chsh -s $(which zsh)

COPY tmux.conf /root/.tmux.conf
COPY vimrc /root/.vimrc

ENV HOME /root
ENV USER root
WORKDIR /root/
