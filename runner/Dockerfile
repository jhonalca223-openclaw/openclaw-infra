# Lightweight ARM64 Runner for OpenClaw Sandbox
# Includes: SSH, Docker CLI, kubectl, Codex CLI, dev tools
FROM alpine:3.21

ARG KUBECTL_VERSION=1.33.6
ARG CODEX_VERSION=0.139.0

# hadolint ignore=DL3018
RUN apk add --no-cache \
    openssh-server \
    openssh-client \
    sudo \
    bash \
    curl \
    git \
    python3 \
    py3-pip \
    docker-cli \
    docker-compose \
    ca-certificates \
    tzdata \
    nodejs \
    npm \
    && rm -rf /var/cache/apk/*

# Install kubectl (ARM64)
RUN curl -fsSL "https://dl.k8s.io/release/v${KUBECTL_VERSION}/bin/linux/arm64/kubectl" -o /usr/local/bin/kubectl \
    && chmod +x /usr/local/bin/kubectl

# Install OpenAI Codex CLI (Rust binary, ARM64 compatible)
# The standalone installer places the binary under /root/.codex/packages/standalone/releases/<version>/bin/codex.
# We copy it to /usr/bin (in PATH for all users on Alpine) so runner can access it.
RUN curl -fsSL https://chatgpt.com/codex/install.sh | CODEX_NON_INTERACTIVE=1 sh \
    && cp /root/.codex/packages/standalone/current/bin/codex /usr/bin/codex \
    && chmod 755 /usr/bin/codex \
    && /usr/bin/codex --version

# Create runner user
RUN adduser -D -s /bin/bash runner \
    && echo "runner ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Setup SSH
RUN mkdir -p /home/runner/.kube /home/runner/.ssh /var/run/sshd \
    && chmod 700 /home/runner/.ssh \
    && chown -R runner:runner /home/runner/.ssh /home/runner/.kube \
    && ssh-keygen -A \
    && sed -i 's/^#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config \
    && sed -i 's/^#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config \
    && echo "AllowUsers runner" >> /etc/ssh/sshd_config

# Workspace directory
RUN mkdir -p /workspace /opt/openclaw \
    && chown -R runner:runner /workspace /opt/openclaw

# Copy SSH authorized keys (mount or bake-in)
COPY authorized_keys /home/runner/.ssh/authorized_keys
RUN chmod 600 /home/runner/.ssh/authorized_keys && chown runner:runner /home/runner/.ssh/authorized_keys

# Set timezone
ENV TZ=America/Santo_Domingo

WORKDIR /workspace

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D", "-e"]
