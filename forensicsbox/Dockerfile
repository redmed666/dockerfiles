ARG HTTP_PROXY=""
ARG HTTPS_PROXY=""
FROM kalilinux/kali-linux-docker:latest

ENV http_proxy=$HTTP_PROXY
ENV https_proxy=$HTTPS_PROXY

###########################
# Install different stuff #
###########################
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
  python3 \
  python-dev \
  python3-dev \
  python-pillow \
  python-pip \
  python3-pip \
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
  tmux \
  libglib2.0-dev \
  libboost-all-dev \
  autoconf \
  libssl-dev \
  libpixman-1-dev \
  libpython-dev \
  python-capstone \
  virtualenv \
  gcc-8 \
  g++-8

################################
# Changes GCC/G++ Version to 8 #
################################
# This is for PyREBox otherwise doesn't compile with gcc/g++ 9
RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 60 && \
  update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-8 60

###################
# Install PEFrame #
###################
RUN cd && \
  git clone https://github.com/guelfoweb/peframe.git && \
  cd peframe && \
  sudo bash install.sh

#######################
# Install Volatility3 #
#######################
RUN unlink /usr/bin/volatility && \
  mv /usr/share/volatility /usr/share/volatility2

RUN cd /tmp && \
  git clone https://github.com/volatilityfoundation/volatility3.git volatility

RUN pip3 install yara-python

RUN mv /tmp/volatility /usr/share/volatility && \ 
  cd /usr/share/volatility && \
  chmod +x vol.py && \
  ln -fs /usr/share/volatility/vol.py /usr/bin/

RUN cd /usr/share/volatility && \
  python3 setup.py install

RUN cd && \
  curl https://downloads.volatilityfoundation.org/volatility3/symbols/windows.zip --output windows.zip && \
  unzip windows.zip && \
  mv windows /usr/share/volatility/volatility/symbols

###################
# Install Radare2 #
###################
RUN cd && \
  git clone https://github.com/radareorg/radare2.git

RUN cd && \
  cd radare2 && \
  ./sys/user.sh

# Install R2 related stuff
RUN pip3 install frida frida-tools r2pipe

# Install node.js => needed for some R2 plugins
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

##################
# Install Qiling #
##################
RUN cd && \
  git clone https://github.com/qilingframework/qiling.git qiling && \
  cd qiling && \
  pip3 install -r requirements.txt && \
  python3 setup.py install

########################
# Install peda for gdb #
########################
RUN cd && \
  git clone https://github.com/longld/peda.git peda &&                                  \
  echo "source /root/peda/peda.py" >> /root/.gdbinit

#####################
# Install oh-my-zsh #
#####################
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
RUN chsh -s $(which zsh)
# Dirty hack in order to have access to source...
RUN mv /bin/sh /bin/sh.bak && \
  ln -sf /bin/zsh /bin/sh

####################
# Install PyREBox3 #
####################
RUN cd && \
  git clone --single-branch --branch python3migration https://github.com/Cisco-Talos/pyrebox.git

RUN cd && \ 
  cd pyrebox/ && \
  virtualenv -p python3 pyrebox3_venv && \
  source pyrebox3_venv/bin/activate && \
  pip3 install -r requirements.txt

RUN cd && \
  cd pyrebox/ && \
  source pyrebox3_venv/bin/activate && \
  ./build.sh

RUN unlink /bin/sh && \
  mv /bin/sh.bak /bin/sh

#####################
# Copy config files #
#####################
RUN cp /usr/local/lib/python3.*/dist-packages/usr/lib/python3/dist-packages/keystone/libkeystone.so /usr/local/lib/python3.*/dist-packages/keystone/
COPY tmux.conf /root/.tmux.conf
COPY vimrc /root/.vimrc
COPY radare2rc /root/.radare2rc

# Expose 5900 => VNC for PyREBox
EXPOSE 5900
RUN mkdir /root/mem
ENV HOME /root
ENV USER root
WORKDIR /root/mem
