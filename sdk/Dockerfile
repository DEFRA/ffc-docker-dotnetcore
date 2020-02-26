# Allow .Net Core version to be set at build time
ARG NETCORE_VERSION=3.1

# Extend Alpine variant of .Net Core SDK base image for small image size
FROM mcr.microsoft.com/dotnet/core/sdk:${NETCORE_VERSION}-alpine

# Default the SDK image to run as development
ENV ASPNETCORE_ENVIRONMENT=development

# Create a dotnet user to run as
RUN addgroup -g 1000 dotnet \
    && adduser -u 1000 -G dotnet -s /bin/sh -D dotnet

# Install dev tools, such as VSCode debugger and its dependencies
RUN apk update \
  && apk --no-cache add curl procps unzip \
  && wget -qO- https://aka.ms/getvsdbgsh | /bin/sh /dev/stdin -v latest -l /vsdbg

# Default to the dotnet user and run from their home folder
USER dotnet
WORKDIR /home/dotnet
