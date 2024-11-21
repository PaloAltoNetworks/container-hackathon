# ╔══════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╗
# ║ Dev Stage -- Individual layers to make finding and fixing issues easier                                                                  ║
# ╚══════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════════╝
FROM eclipse-temurin:17-jdk-jammy AS base-dev 

WORKDIR /app

RUN apt-get update
RUN apt-get install --no-install-recommends -y curl
RUN apt-get install --no-install-recommends -y git
RUN apt-get install --no-install-recommends -y maven

# Updating package to remove vulnerability
# CVE: CVE-2021-26291
# Package: org.apache.maven_maven-compat | org.apache.maven_maven-core
# Vulnerable Version: 3.6.3
RUN curl -fsSL https://archive.apache.org/dist/maven/maven-3/3.8.1/binaries/apache-maven-3.8.1-bin.tar.gz -o /tmp/maven.tar.gz
RUN tar xzvf /tmp/maven.tar.gz -C /tmp
RUN cp -f /tmp/apache-maven-3.8.1/lib/maven-core-3.8.1.jar /usr/share/maven-repo/org/apache/maven/maven-core/3.x/maven-core-3.x.jar
RUN cp -f /tmp/apache-maven-3.8.1/lib/maven-compat-3.8.1.jar /usr/share/maven-repo/org/apache/maven/maven-compat/3.x/maven-compat-3.x.jar
RUN rm -rf /tmp/*

# Updating package to remove vulnerability
# CVE: CVE-2022-29599
# Package: org.apache.maven.shared_maven-shared-utils
# Vulnerable Version: 3.3.0
RUN curl -fsSL https://repo1.maven.org/maven2/org/apache/maven/shared/maven-shared-utils/3.3.3/maven-shared-utils-3.3.3.jar -o /tmp/maven-shared-utils-3.3.3.jar
RUN cp -f /tmp/maven-shared-utils-3.3.3.jar /usr/share/maven-repo/org/apache/maven/shared/maven-shared-utils/debian/maven-shared-utils-debian.jar
RUN mv -f /tmp/maven-shared-utils-3.3.3.jar /usr/share/maven-repo/org/apache/maven/shared/maven-shared-utils/3.3.0/maven-shared-utils-3.3.3.jar

# # # Removing vulnerable android package not in use CVE-2023-2976
# # RUN rm /opt/apache-maven-3.8.1/lib/guava-25.1-android.jar

# # Updating package to remove vulnerability
# # CVE: CVE-2024-47554
# # Package: commons-io
# # Vulnerable Version: 2.6
# RUN curl -fsSL https://repo1.maven.org/maven2/commons-io/commons-io/2.14.0/commons-io-2.14.0.jar -o /tmp/commons-io-2.14.0.jar
# RUN rm /opt/apache-maven-3.8.1/lib/commons-io-2.5.jar
# RUN mv /tmp/commons-io-2.14.0.jar /opt/apache-maven-3.8.1/lib/

# # Updating package to remove vulnerability
# # CVE: CVE-2024-47554
# # Package: wagon-http-shadded ---> depends on commons-io-2.6
# # Vulnerable Version: 3.4.3
# RUN curl -fsSL https://repo1.maven.org/maven2/org/apache/maven/wagon/wagon-http/3.5.3/wagon-http-3.5.3-shaded.jar -o /tmp/wagon-http-3.5.3-shaded.jar
# RUN rm /opt/apache-maven-3.8.1/lib/wagon-http-3.4.3-shaded.jar
# RUN mv /tmp/wagon-http-3.5.3-shaded.jar /opt/apache-maven-3.8.1/lib/wagon-http-3.5.3-shaded.jar

# # Updating package to remove vulnerability
# # CVE: CVE-2021-37714
# # Package: org.jsoup_jsoup
# # Vulnerable Version: 1.12.1
# RUN curl -fsSL https://repo1.maven.org/maven2/org/jsoup/jsoup/1.14.2/jsoup-1.14.2.jar -o /tmp/jsoup-1.14.2.jar
# RUN rm /opt/apache-maven-3.8.1/lib/jsoup-1.12.1.jar
# RUN mv /tmp/jsoup-1.14.2.jar /opt/apache-maven-3.8.1/lib/jsoup-1.14.2.jar

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
    && curl -fsSL https://archive.apache.org/dist/maven/maven-3/3.8.1/binaries/apache-maven-3.8.1-bin.tar.gz -o /tmp/maven.tar.gz \
    && tar xzvf /tmp/maven.tar.gz -C /tmp \
    && cp -f /tmp/apache-maven-3.8.1/lib/maven-core-3.8.1.jar /usr/share/maven-repo/org/apache/maven/maven-core/3.x/maven-core-3.x.jar \
    && cp -f /tmp/apache-maven-3.8.1/lib/maven-compat-3.8.1.jar /usr/share/maven-repo/org/apache/maven/maven-compat/3.x/maven-compat-3.x.jar \
    # Update Maven dependencies
    # && cd /tmp/apache-maven-3.8.1/lib \
    # && rm maven-shared-utils-3.2.1.jar guava-25.1-android.jar commons-io-2.5.jar wagon-http-3.4.3-shaded.jar jsoup-1.12.1.jar \
    && curl -fsSL https://repo1.maven.org/maven2/org/apache/maven/shared/maven-shared-utils/3.3.3/maven-shared-utils-3.3.3.jar -o /tmp/maven-shared-utils-3.3.3.jar  \
    && cp -f /tmp/maven-shared-utils-3.3.3.jar /usr/share/maven-repo/org/apache/maven/shared/maven-shared-utils/debian/maven-shared-utils-debian.jar \
    && cp -f /tmp/maven-shared-utils-3.3.3.jar /usr/share/maven-repo/org/apache/maven/shared/maven-shared-utils/3.3.0/maven-shared-utils-3.3.3.jar \
    # && curl -fsSL -O https://repo1.maven.org/maven2/commons-io/commons-io/2.14.0/commons-io-2.14.0.jar \
    # && curl -fsSL -O https://repo1.maven.org/maven2/org/apache/maven/wagon/wagon-http/3.5.3/wagon-http-3.5.3-shaded.jar \
    # && curl -fsSL -O https://repo1.maven.org/maven2/org/jsoup/jsoup/1.14.2/jsoup-1.14.2.jar \
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