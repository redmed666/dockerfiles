ARG HTTP_PROXY=""
ARG HTTPS_PROXY=""
FROM ubuntu:bionic

ENV http_proxy=$HTTP_PROXY
ENV https_proxy=$HTTPS_PROXY

RUN useradd -m retdec
WORKDIR /home/retdec
ENV HOME /home/retdec

RUN apt-get -y update && \
	DEBIAN_FRONTEND=noninteractive apt-get install -y                                   \
	build-essential                                                                     \
	cmake                                                                               \
	git                                                                                 \
	perl                                                                                \
	python3                                                                             \
	bash                                                                                \
	coreutils                                                                           \
	wget                                                                                \
	bc                                                                                  \
	doxygen                                                                             \
	graphviz                                                                            \
	upx                                                                                 \
	flex                                                                                \
	bison                                                                               \
	zlib1g-dev                                                                          \
	libtinfo-dev                                                                        \
	autoconf                                                                            \
	automake                                                                            \
	pkg-config                                                                          \
	m4                                                                                  \
	libtool						                                    \
	curl 						                                    \
	gcc 						                                    \
	git 						                                    \
	bison 						                                    \
	pkg-config 					                                    \
	make 						                                    \
	gnupg2                                                                              \
        python                                                                              \
        vim                                                                                 \
        python-pip                                                                          \
        python3-pip

USER retdec
RUN git clone https://github.com/avast-tl/retdec &&                                         \
	cd retdec &&                                                                        \
	mkdir build &&                                                                      \
	cd build &&                                                                         \
	cmake .. -DCMAKE_INSTALL_PREFIX=/home/retdec/retdec-install &&                      \
	make -j$(nproc) && \
	make install && \
	cd 

RUN git clone https://github.com/radare/radare2.git &&                                      \
	cd radare2 &&                                                                       \
	./sys/user.sh &&                                                                    \
        cd

RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash && \
    export NVM_DIR="$HOME/.nvm" &&                                                          \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  &&                                    \
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" &&                   \
    nvm install 10.5.0 && \
    nvm use node

ENV PATH /home/retdec/retdec-install/bin:/home/retdec/bin:/home/retdec/.nvm/versions/node/v10.5.0/bin/:$PATH

RUN pip install frida frida-tools

RUN r2pm init &&                                                                            \
    r2pm update &&                                                                          \
    r2pm -i r2retdec &&                                                                     \
    echo `which retdec-decompiler.sh` >> ~/.r2retdec &&                                     \
    r2pm -i r2dec &&                                                                        \
    r2pm -i r2msdn &&                                                                       \
    r2pm -i r2frida

CMD ["/bin/bash"]
