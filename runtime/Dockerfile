# Allow .Net Core version to be set at build time
ARG NETCORE_VERSION=3.1

# Extend Alpine variant of ASP.net base image for small image size
FROM mcr.microsoft.com/dotnet/core/aspnet:${NETCORE_VERSION}-alpine

# Default the runtime image to run as production
ENV ASPNETCORE_ENVIRONMENT=production

# Create a dotnet user to run as
RUN addgroup -g 1000 dotnet \
    && adduser -u 1000 -G dotnet -s /bin/sh -D dotnet

# Default to the dotnet user and run from their home folder
USER dotnet
WORKDIR /home/dotnet
