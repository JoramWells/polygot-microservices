FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies with pinned versions
RUN apt-get update && apt-get install -y \
    curl=7.81.0-1ubuntu1.18 \
    git=1:2.34.1-1ubuntu1.12 \
    vim=2:8.2.3995-1ubuntu2.19 \
    nano=6.2-1 \
    wget=1.21.2-2ubuntu1.1 \
    tree=2.0.2-1 \
    jq=1.6-2.1ubuntu3 \
    ca-certificates=20230311ubuntu0.22.04.1 \
    gnupg=2.2.27-3ubuntu2.1 \
    lsb-release=11.1.0ubuntu4 \
    software-properties-common=0.99.22.9 \
    && rm -rf /var/lib/apt/lists/*

# Install Python 3.10 with pinned version
RUN apt-get update && apt-get install -y \
    python3=3.10.6-1~22.04 \
    python3-pip=22.0.2+dfsg-1ubuntu0.4 \
    python3-venv=3.10.6-1~22.04 \
    && rm -rf /var/lib/apt/lists/*

# Install Node.js 18.x with pinned version
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
    && apt-get install -y nodejs=18.20.4-1nodesource1 \
    && rm -rf /var/lib/apt/lists/*

# Install Go 1.18 with pinned version
RUN wget https://go.dev/dl/go1.18.10.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf go1.18.10.linux-amd64.tar.gz \
    && rm go1.18.10.linux-amd64.tar.gz

# Set up Go environment variables
ENV PATH="/usr/local/go/bin:${PATH}"
ENV GOPATH="/go"
ENV PATH="${GOPATH}/bin:${PATH}"

# Install Docker CLI (for docker-compose documentation)
RUN curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg \
    && echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
    | tee /etc/apt/sources.list.d/docker.list > /dev/null \
    && apt-get update \
    && apt-get install -y docker-ce-cli=5:24.0.7-1~ubuntu.22.04~jammy \
    && rm -rf /var/lib/apt/lists/*

# Install Docker Compose with pinned version
RUN curl -L "https://github.com/docker/compose/releases/download/v2.20.0/docker-compose-$(uname -s)-$(uname -m)" \
    -o /usr/local/bin/docker-compose \
    && chmod +x /usr/local/bin/docker-compose

# Create workspace directory
WORKDIR /workspace

# Copy the microservices project structure into the container
COPY project-structure/ /workspace/

# Install Python dependencies for user-service
WORKDIR /workspace/services/user-service
RUN pip3 install --no-cache-dir -r requirements.txt

# Install Node.js dependencies for order-service
WORKDIR /workspace/services/order-service
RUN npm install --omit=dev

# Build Go service to verify it compiles
WORKDIR /workspace/services/inventory-service
RUN go mod download && go mod verify
RUN go build -o inventory-service .

# Set working directory back to project root
WORKDIR /workspace

# Default command
CMD ["/bin/bash"]