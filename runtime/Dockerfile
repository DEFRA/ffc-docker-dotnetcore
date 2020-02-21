ARG NETCORE_VERSION=3.1

FROM mcr.microsoft.com/dotnet/core/aspnet:${NETCORE_VERSION}-alpine
ENV ASPNETCORE_ENVIRONMENT=production
RUN addgroup -g 1000 dotnet \
    && adduser -u 1000 -G dotnet -s /bin/sh -D dotnet
USER dotnet
WORKDIR /home/dotnet
ENTRYPOINT [ "dotnet" ]
