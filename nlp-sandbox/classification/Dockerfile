# tags : https://hub.docker.com/r/tensorflow/tensorflow/tags/
FROM tensorflow/tensorflow:1.13.1-py3-jupyter

# apt
RUN sed -i.bak -e 's#http://\(archive\|security\).ubuntu.com/ubuntu#http://www.ftp.ne.jp/Linux/packages/ubuntu/archive/#g' /etc/apt/sources.list
RUN apt-get update
RUN apt-get install -y file locales man less vim-nox

# locale & timezone
ENV LANG en_US.UTF-8
RUN /usr/sbin/locale-gen ${LANG}
ENV TZ Asia/Tokyo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# tools
RUN apt-get install -y git emacs-nox make curl xz-utils file

# mecab (python2)
RUN apt-get install -y mecab mecab-ipadic-utf8 mecab-jumandic-utf8 unidic-mecab libmecab-dev python-mecab

# neologd
# RUN curl -L https://github.com/neologd/mecab-ipadic-neologd/archive/master.tar.gz |tar zxf - && \
RUN git clone https://github.com/neologd/mecab-ipadic-neologd.git && \
    (cd mecab-ipadic-neologd && \
      ./bin/install-mecab-ipadic-neologd -n -y -p /usr/share/mecab/dic/mecab-ipadic-neologd && \
      update-alternatives --install /var/lib/mecab/dic/debian mecab-dictionary /usr/share/mecab/dic/mecab-ipadic-neologd 120) && \
    rm -rf mecab-ipadic-neologd

# dataset
# Livedoor news corpus : see https://www.rondhuit.com/download.html for license.
RUN mkdir -p dataset/livedoor-news/xml && \
    curl -O https://www.rondhuit.com/download/livedoor-news-data.tar.gz && \
    tar xvzf livedoor-news-data.tar.gz -C dataset/livedoor-news/xml
RUN curl -O https://www.rondhuit.com/download/ldcc-20140209.tar.gz && \
    tar xvzf ldcc-20140209.tar.gz -C dataset/livedoor-news

# python3
RUN apt-get install -y libpython3-all-dev python3-all-dev swig
RUN pip3 --no-cache-dir install --upgrade pip

# mecab and others (python3)
RUN pip3 --no-cache-dir install mecab-python3 gensim keras graphviz pydot

# workaround for https://github.com/SamuraiT/mecab-python3/issues/19
RUN pip3 freeze|grep -F 'mecab-python3==0.996.1' && \
        git clone https://github.com/SamuraiT/mecab-python3.git && \
        cd mecab-python3 && git checkout aaac65033963474d02b3c8a9bb27c6e6fa214161 && \
        pip3 install .

# Vowpal Wabbit
RUN apt-get install -y libboost-program-options-dev build-essential zlib1g-dev clang
RUN curl -O -L https://github.com/JohnLangford/vowpal_wabbit/archive/8.4.0.tar.gz && \
    tar xzf 8.4.0.tar.gz && \
    cd vowpal_wabbit-8.4.0  && \
    curl -o vowpalwabbit/vwdll.cpp \
      https://raw.githubusercontent.com/JohnLangford/vowpal_wabbit/bbe14ba1fd70bdde26c0739980d5cadb735ddded/vowpalwabbit/vwdll.cpp && \
    ./configure --with-boost-libdir=/usr/lib/x86_64-linux-gnu CXX=clang++ && \
    make CXX=clang++ && \
    make CXX=clang++ test && \
    make CXX=clang++ install && \
    ldconfig

# fastText
RUN git clone https://github.com/facebookresearch/fastText.git && \
    cd fastText && git checkout v0.2.0 && \
    make && install -m 755 fasttext /usr/local/bin/ && \
    pip3 install .

