FROM ubuntu:16.04

# ------------------------------------------------------------------------------------Basic Linux Packages------------------------------------------------------------------------------------
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    curl \
    wget \
    unzip \
    libunwind8 \
    apt-transport-https \
    ca-certificates \
    libicu55

# ------------------------------------------------------------------------------------Azure Functions and AZ CLI---------------------------------------------------------------------------------------------
RUN wget -q --no-check-certificate https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb && \
    dpkg -i packages-microsoft-prod.deb && \
    apt-get update && \
    apt-get install azure-functions-core-tools-3 && \
    curl -sL https://aka.ms/InstallAzureCLIDeb | bash
