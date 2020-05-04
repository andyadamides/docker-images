FROM ubuntu:16.04

# ------------------------------------------------------------------------------------Basic Linux Packages------------------------------------------------------------------------------------
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    wget \
    unzip \
    libunwind8 \
    libicu55

# ------------------------------------------------------------------------------------SQL Packaga---------------------------------------------------------------------------------------------
RUN mkdir ~/sqlpackage && \
    wget https://go.microsoft.com/fwlink/?linkid=2128144 --no-check-certificate && \
    unzip index.html?linkid=2128144 -d ~/sqlpackage && \
    echo "export PATH=\"\$PATH:$HOME/sqlpackage\"" >> ~/.bashrc && \
    chmod a+x ~/sqlpackage/sqlpackage && \
    mv ~/sqlpackage/sqlpackage /usr/bin && \
    rm /index.html*
