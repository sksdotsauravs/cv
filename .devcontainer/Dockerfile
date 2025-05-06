FROM debian:bookworm-slim

# TeXLive installer URL
ARG TEXLIVE_INSTALLER_URL=https://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz

# Set workspace
WORKDIR /workspaces

# Install some dependencies and font packages
RUN set -eux; \
  apt-get update && \
  apt-get install --yes --no-install-recommends \
    ca-certificates \
    curl \
    fontconfig \
    fonts-lato \
    fonts-roboto-slab \
    gnupg \
    locales \
    perl \
    tzdata \
    wget \
    xz-utils && \
  fc-cache -f -v && \
  rm -rf /var/lib/apt/lists/* && \
  ln -fs /usr/share/zoneinfo/Europe/Berlin /etc/localtime && \
  dpkg-reconfigure -f noninteractive tzdata && \
  echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
  locale-gen

# Install TeXLive using minimal profile
RUN set -eux; \
  mkdir -p /install-tl && \
  cd /install-tl && \
  curl -sSL "$TEXLIVE_INSTALLER_URL" | tar -xz --strip-components=1 && \
  echo "selected_scheme scheme-small" > texlive.profile && \
  ./install-tl -profile texlive.profile && \
  rm -rf /install-tl

# Detect installed version TexLive version and run tlmgr
# to update the package manager and install additional packages
RUN set -eux; \
  TEXLIVE_DIR="/usr/local/texlive/$(ls /usr/local/texlive | grep -E '^[0-9]{4}$')" && \
  export PATH="$TEXLIVE_DIR/bin/x86_64-linux:$PATH" && \
  tlmgr update --self && \
  tlmgr install \
    accsupp \
    adjustbox \
    biber \
    biblatex \
    changepage \
    csquotes \
    dashrule \
    enumitem \
    environ \
    fontawesome5 \
    ifmtarg \
    latexmk \
    multirow \
    paracol \
    pdfx \
    tcolorbox \
    tikzfill \
    titlesec \
    xmpincl

# Set PATH for TexLive in /etc/profile.d to make it available in the container
# and in the VSCode terminal
RUN set -eux; \
  TEXLIVE_DIR="/usr/local/texlive/$(ls /usr/local/texlive | grep -E '^[0-9]{4}$')" && \
  TEXLIVE_PATH="$TEXLIVE_DIR/bin/x86_64-linux" && \
  echo "export PATH=$TEXLIVE_PATH:\$PATH" > /etc/profile.d/texlive.sh

# Set environment variables for locale and timezone
ENV LANG=en_US.UTF-8 \
  LANGUAGE=en_US:en \
  LC_ALL=en_US.UTF-8 \
  TZ=Europe/Berlin