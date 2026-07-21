FROM chinachu/mirakurun

# ビルド環境構築
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    git \
    autoconf \
    automake \
    && rm -rf /var/lib/apt/lists/*

# デコーダーのインストール
WORKDIR /tmp
RUN wget -nv https://github.com/tsukumijima/libaribb25/releases/download/v0.2.10/libaribb25_0.2.10_amd64.deb && \
    apt-get install -y --no-install-recommends ./libaribb25_0.2.10_amd64.deb

# recpt1のインストール
WORKDIR /tmp
RUN git clone --depth=1 https://github.com/stz2012/recpt1.git
WORKDIR /tmp/recpt1/recpt1
RUN ./autogen.sh && \
    ./configure && \
    make -j "$(nproc)" && \
    make install && \
    make clean

WORKDIR /app
