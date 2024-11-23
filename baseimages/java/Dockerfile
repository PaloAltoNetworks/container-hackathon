# ╔══════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╗
# ║ Dev Stage -- Individual layers to make finding and fixing issues easier                                                                  ║
# ╚══════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╝
FROM eclipse-temurin:17-jdk-jammy AS base-dev 

WORKDIR /app

RUN apt-get update
RUN apt-get install --no-install-recommends -y curl
RUN apt-get install --no-install-recommends -y git
RUN apt-get install --no-install-recommends -y maven

# Security: Create a non-root user
ARG USER_ID=1000
ARG GROUP_ID=1000
RUN groupadd -g ${GROUP_ID} appuser
RUN useradd -u ${USER_ID} -g appuser -s /bin/bash -m appuser
RUN mkdir -p /app/target && chown -R appuser:appuser /app
USER appuser

# ╔══════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╗
# ║ Prod Stage -- Combine commands into fewer layers for faster build times and better layer caching                                         ║
# ╚══════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╝
FROM eclipse-temurin:17-jdk-jammy AS prod

WORKDIR /app

# Install dependencies and Maven in a single layer
RUN apt-get update && apt-get install --no-install-recommends -y \
    git \
    curl \
    maven \
    # Clean up
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/*

# Security: Create a non-root user
ARG USER_ID=1000
ARG GROUP_ID=1000
RUN groupadd -g ${GROUP_ID} appuser \
    && useradd -u ${USER_ID} -g appuser -s /bin/bash -m appuser \
    && mkdir -p /app/target \
    && chown -R appuser:appuser /app
USER appuser