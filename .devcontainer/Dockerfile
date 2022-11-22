FROM vborja/asdf-ubuntu:latest

# Docker環境下でのPATH設定
ENV PATH /usr/local/bin:$PATH
# apt-get installする際のフロントエンドエラー対策
ENV DEBIAN_FRONTEND=noninteractive
# apt-get installする際に表示されるエラーメッセージ(次行)の抑止
# debconf: delaying package configuration, since apt-utils is not installed
ENV DEBCONF_NOWARNINGS yes
# ロケールを日本語UTF-8に設定
# これによりDocker環境下で日本語入力が可能となる 
USER root
RUN apt-get update \
    && apt-get install -y locales \
    && locale-gen ja_JP.UTF-8
# ロケール環境変数の設定
ENV LANG ja_JP.UTF-8
ENV LANGUAGE ja_JP:ja
ENV LC_ALL ja_JP.UTF-8
# apt-getした際のmanパッケージインストールエラーの回避策と
# manコマンドのインストール
RUN rm /etc/dpkg/dpkg.cfg.d/excludes \
    && apt-get update \
    && apt-get install -y man;
# zshのインストールと環境整備用パッケージのインストール
RUN apt-get update \
    && apt-get install -y zsh vim sed git

USER asdf
# zshの実行
RUN zsh
# 環境変数の設定
ENV SHELL /usr/bin/zsh

USER root
# ログインシェルを指定(アカウント情報の作成) 
RUN sed -i.bak "s|$HOME:.*|$HOME:$SHELL|" /etc/passwd

USER asdf
# preztoをクローンしてくる
RUN git clone --recursive \
    https://github.com/sorin-ionescu/prezto.git \
    $HOME/.zprezto
# preztoの設定ファイルのシンボリックリンクを
# ホームディレクトリに張る
RUN ln -s $HOME/.zprezto/runcoms/zlogin    $HOME/.zlogin \
    && ln -s $HOME/.zprezto/runcoms/zlogout   $HOME/.zlogout \
    && ln -s $HOME/.zprezto/runcoms/zpreztorc $HOME/.zpreztorc \
    && ln -s $HOME/.zprezto/runcoms/zprofile  $HOME/.zprofile \
    && ln -s $HOME/.zprezto/runcoms/zshenv    $HOME/.zshenv \
    && ln -s $HOME/.zprezto/runcoms/zshrc     $HOME/.zshrc
# LANG環境変数のエクスポートコマンドを.zshrcに出力
RUN echo "export LANG=ja_JP.UTF-8" >> $HOME/.zshrc

# # asdfの環境変数のエクスポートコマンドを.zshrcに出力
# RUN echo '\n
# # ASDF \n
# . $HOME/.asdf/asdf.sh \n
# fpath=(${ASDF_DIR}/completions $fpath) \n
# # 補完 \n
# autoload -Uz compinit \n
# compinit' >> ~/.zshrc

# lsコマンドの色指定用設定をファイルに出力
RUN dircolors -p > $HOME/.dircolors
# lsコマンドのファイル属性
# "ディレクトリ", "シンボリックリンク", "実行ファイル"の表示色を指定
RUN sed -i "s|DIR 01;34|DIR 01;36|" $HOME/.dircolors \
    && sed -i "s|LINK 01;36|LINK 01;33|" $HOME/.dircolors \
    && sed -i "s|EXEC 01;32|EXEC 01;34|" $HOME/.dircolors
# preztoのprompt(表示テーマ)をzshrcで指定
# lsコマンドの色指定ファイルの読み込みとlsコマンドのエイリアスを設定
# sedで-iオプションを使いシンボリックリンクを編集するとリンクが切れてしまうため
# リンク元ファイルを直接編集している
# RUN echo "\nprompt fade red\n" >> $HOME/.zprezto/runcoms/zshrc \
#     && echo "eval \`dircolors --sh $HOME/.dircolors\`" >> $HOME/.zprezto/runcoms/zshrc \
#     && sed -i "/^eval LS_COLORS='';/d" $HOME/.zprezto/runcoms/zshrc \
#     && sed -i "/^export LS_COLORS/d" $HOME/.zprezto/runcoms/zshrc \
#     && echo "alias ls='ls --color=auto'\n" >> $HOME/.zprezto/runcoms/zshrc

# preztoのシンタックスハイライトを有効にするため設定をzpreztorcに出力
# sedで-iオプションを使いシンボリックリンクを編集するとリンクが切れてしまうため
# リンク元ファイルを直接編集している
RUN sed -i "s|'completion' \\\\|'completion' \\\\\n  'syntax-highlighting' \\\\|" $HOME/.zprezto/runcoms/zpreztorc \
    && sed -i "s|Syntax Highlighting\n#\n|Syntax Highlighting\n#\nzstyle ':prezto:module:syntax-highlighting' color 'yes'\n|" $HOME/.zprezto/runcoms/zpreztorc

ADD java .asdf/toolset/java
USER root
RUN bash .asdf/toolset/java/build-deps
USER asdf
RUN asdf-install-toolset java

ADD golang .asdf/toolset/golang
USER root
RUN bash .asdf/toolset/golang/build-deps
USER asdf
RUN asdf-install-toolset golang

ADD nodejs .asdf/toolset/nodejs
USER root
RUN bash .asdf/toolset/nodejs/build-deps
USER asdf
RUN asdf-install-toolset nodejs

ADD yarn .asdf/toolset/yarn
USER root
RUN bash .asdf/toolset/yarn/build-deps
USER asdf
RUN asdf-install-toolset yarn

ADD python .asdf/toolset/python
USER root
RUN bash .asdf/toolset/python/build-deps
USER asdf
RUN asdf-install-toolset python

ADD poetry .asdf/toolset/poetry
USER root
RUN bash .asdf/toolset/poetry/build-deps
USER asdf
RUN asdf-install-toolset poetry

ADD github-cli .asdf/toolset/github-cli
USER root
RUN bash .asdf/toolset/github-cli/build-deps
USER asdf
RUN asdf-install-toolset github-cli

ADD ghq .asdf/toolset/ghq
USER root
RUN bash .asdf/toolset/ghq/build-deps
USER asdf
RUN asdf-install-toolset ghq

ADD fzf .asdf/toolset/fzf
USER root
RUN bash .asdf/toolset/fzf/build-deps
USER asdf
RUN asdf-install-toolset fzf

ADD ripgrep .asdf/toolset/ripgrep
USER root
RUN bash .asdf/toolset/ripgrep/build-deps
USER asdf
RUN asdf-install-toolset ripgrep

ADD bat .asdf/toolset/bat
USER root
RUN bash .asdf/toolset/bat/build-deps
USER asdf
RUN asdf-install-toolset bat

ADD jq .asdf/toolset/jq
USER root
RUN bash .asdf/toolset/jq/build-deps
USER asdf
RUN asdf-install-toolset jq

ADD neovim .asdf/toolset/neovim
USER root
RUN bash .asdf/toolset/neovim/build-deps
USER asdf
RUN asdf-install-toolset neovim

USER root
# RUN apt-get update && apt-get upgrade && apt-get install -y sudo
RUN curl https://cli-assets.heroku.com/install-ubuntu.sh | sh
USER asdf
RUN npm install sfdx-cli --global


