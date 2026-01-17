FROM debian:bookworm-slim AS builder

# ビルド環境構築
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    make \
    gcc \
    g++ \
    liblua5.2-dev \
    lua-zlib \
    curl \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# EDCBのビルド
WORKDIR /tmp
RUN git clone -b work-plus-s-251101 --depth=1 \
    https://github.com/xtne6f/EDCB.git
WORKDIR /tmp/EDCB/Document/Unix
RUN make -j "$(nproc)" && \
    make install && \
    make extra -j "$(nproc)" && \
    make install_extra && \
    mkdir -p /var/local/edcb && \
    make setup_ini && \
    sed -i -e 's/^ALLOW_SETTING=.*/ALLOW_SETTING=true/' \
        /var/local/edcb/HttpPublic/legacy/util.lua

# EDCB Material WebUIのインストール
WORKDIR /tmp
RUN git clone --depth=1 https://github.com/EMWUI/EDCB_Material_WebUI.git
WORKDIR /tmp/EDCB_Material_WebUI
RUN cp -r HttpPublic /var/local/edcb/ && \
    cp -r Setting /var/local/edcb/

# BonDriver_LinuxMirakcのビルド
WORKDIR /tmp
RUN git clone --depth=1 --recurse-submodules \
    https://github.com/matching/BonDriver_LinuxMirakc.git
WORKDIR /tmp/BonDriver_LinuxMirakc
RUN make -j "$(nproc)" && \
    cp BonDriver_LinuxMirakc.so /usr/local/lib/edcb/ && \
    cp BonDriver_LinuxMirakc.so.ini_sample \
        /usr/local/lib/edcb/BonDriver_LinuxMirakc.so.ini

FROM debian:bookworm-slim AS runtime

# ランタイム依存関係のみインストール
RUN apt-get update && apt-get install -y --no-install-recommends \
    liblua5.2-dev \
    lua-zlib \
    ffmpeg \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# ビルドステージから必要なファイルをコピー
COPY --from=builder /usr/local/bin/ /usr/local/bin/
COPY --from=builder /usr/local/lib/edcb/ /usr/local/lib/edcb/
RUN mkdir -p /var/local/edcb && \
    chmod 777 /var/local/edcb
COPY --from=builder /var/local/edcb/ /var/local/edcb/

# デバッグ用に移動しておく
WORKDIR /var/local/edcb

# entrypoint.shをコピーして実行権限付与
COPY ./entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh

CMD [ "/usr/local/bin/entrypoint.sh" ]
