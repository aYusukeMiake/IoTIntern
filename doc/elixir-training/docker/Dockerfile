FROM jupyter/base-notebook

USER root

ENV WORK_DIR=/opt
ENV ERL_TOP=$WORK_DIR/otp \
    ELIXIR_TOP=$WORK_DIR/elixir
ENV PATH=$ERL_TOP/target/bin:$ELIXIR_TOP/bin:$PATH \
    ERL_VERSION=23.1.2 \
    ELIXIR_VERSION=1.12.3

RUN set -xe \
  && apt-get update \
  && apt install -y --no-install-recommends \
    git \
    curl \
    wget \
    unzip \
    autoconf \
    g++ \
    gcc \
    libncurses5-dev \
    libncursesw5-dev \
    libssl-dev \
    libxslt-dev \
    libwxbase3.0-dev \
    make \
    gcc-mingw-w64

RUN mkdir -p $WORK_DIR \
  && cd $WORK_DIR \
  && wget https://github.com/erlang/otp/archive/OTP-$ERL_VERSION.zip -O otp.zip \
  && unzip otp.zip -d $WORK_DIR \
  && mv $WORK_DIR/otp-OTP-$ERL_VERSION $ERL_TOP \
  && rm otp.zip

RUN cd $ERL_TOP \
  && ./otp_build autoconf \
  && ./configure --prefix=$ERL_TOP/target \
  && make \
  && make install \
  && cd $WORK_DIR \
  && wget https://github.com/elixir-lang/elixir/releases/download/v$ELIXIR_VERSION/Precompiled.zip -O elixir.zip \
  && unzip elixir.zip -d $ELIXIR_TOP \
  && rm elixir.zip

RUN apt install -y \
    libzmq3-dev \
    libsqlite3-dev

RUN set -xe \
  && curl -s https://api.github.com/repos/pprzetacznik/IElixir/releases/latest | grep "tarball_url" | sed -n -e 's/.*tarball_url": "\(.*\)".*/\1/p' | xargs -t curl -fSL -o ielixir.tar.gz \
  && mkdir ielixir \
  && tar -zxvf ielixir.tar.gz -C ielixir --strip-components=1 \
  && rm ielixir.tar.gz \
  && cd ielixir \
  && ls -alh \
  && mix local.hex --force \
  && mix local.rebar --force \
  && mix deps.get \
  && mix deps.compile \
  && ./install_script.sh \
  && conda install --quiet --yes 'jupyter_console'

USER $NB_UID

CMD ["start-notebook.sh"]

