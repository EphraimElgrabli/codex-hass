ARG BUILD_FROM
FROM ${BUILD_FROM}

LABEL maintainer="Kecks Digital"
LABEL org.opencontainers.image.title="Codex for Home Assistant"
LABEL org.opencontainers.image.description="OpenAI Codex for the Home Assistant sidebar with persistent terminal sessions and optional MCP access"
LABEL org.opencontainers.image.source="https://github.com/kecksdigital/codex-hass"
LABEL org.opencontainers.image.licenses="MIT"

ENV \
    LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    TERM=xterm-256color

RUN apk add --no-cache \
    acl \
    bash \
    ca-certificates \
    coreutils \
    curl \
    findutils \
    gawk \
    git \
    github-cli \
    grep \
    jq \
    nano \
    ncurses \
    ncurses-terminfo-base \
    nodejs \
    npm \
    openssh-client \
    openssl \
    p7zip \
    ripgrep \
    sed \
    sudo \
    su-exec \
    tmux \
    vim \
    libstdc++ \
    && update-ca-certificates

ARG BUILD_ARCH
RUN case "${BUILD_ARCH}" in \
        amd64) TTYD_ARCH="x86_64" ;; \
        aarch64) TTYD_ARCH="aarch64" ;; \
        *) TTYD_ARCH="x86_64" ;; \
    esac && \
    curl -fsSL "https://github.com/tsl0922/ttyd/releases/download/1.7.7/ttyd.${TTYD_ARCH}" -o /usr/bin/ttyd && \
    chmod +x /usr/bin/ttyd

RUN npm install -g @openai/codex \
    && codex --version

RUN pip3 install --no-cache-dir --break-system-packages hass-mcp

RUN case "${BUILD_ARCH}" in \
        amd64) HA_ARCH="amd64" ;; \
        aarch64) HA_ARCH="aarch64" ;; \
        *) HA_ARCH="amd64" ;; \
    esac && \
    curl -fsSL "https://github.com/home-assistant/cli/releases/latest/download/ha_${HA_ARCH}" -o /usr/local/bin/ha && \
    chmod +x /usr/local/bin/ha

RUN adduser -D -H -u 22000 -G root codex && \
    mkdir -p \
    /data/codex-home \
    /data/codex-managed \
    /homeassistant \
    /root/.local/bin

COPY rootfs/ /

RUN chmod +x \
    /usr/local/bin/hass-mcp-wrapper \
    /usr/local/bin/hass-mcp-root-helper \
    /usr/local/bin/codex-merge-config \
    /usr/local/bin/codex-shell \
    /usr/local/bin/codex-session \
    /usr/local/bin/codex-start \
    && chmod 440 /etc/sudoers.d/codex-hass-mcp

RUN cat > /etc/tmux.conf << 'EOF'
set -g history-limit 20000
set -ga terminal-overrides ',xterm*:smcup@:rmcup@'
set -g mouse on
bind -n WheelUpPane if-shell -F -t = "#{mouse_any_flag}" "send-keys -M" "if -Ft= '#{pane_in_mode}' 'send-keys -M' 'select-pane -t=; copy-mode -e; send-keys -M'"
bind -n WheelDownPane select-pane -t= \; send-keys -M
EOF

RUN ln -sf /etc/tmux.conf /root/.tmux.conf

RUN cat > /etc/codex.bashrc << 'EOF'
export TERM=xterm-256color
export LANG=C.UTF-8
export CODEX_HOME="${CODEX_HOME:-$HOME/.codex}"
PS1='\[\033[1;36m\]codex\[\033[0m\]:\[\033[1;34m\]\w\[\033[0m\]\$ '

alias ll='ls -la'
alias ha-config='cd /homeassistant'
alias ha-logs='cat /homeassistant/home-assistant.log 2>/dev/null || echo "Log not found"'
EOF

RUN echo 'source /etc/codex.bashrc' > /root/.profile

ENV PATH="/root/.local/bin:${PATH}"

WORKDIR /homeassistant

HEALTHCHECK --interval=30s --timeout=10s --start-period=10s --retries=3 \
    CMD curl -sf http://localhost:7681/ >/dev/null || exit 1

ENTRYPOINT []
CMD ["/usr/local/bin/codex-start"]
