ARG NETCORE_VERSION=3.1

FROM mcr.microsoft.com/dotnet/core/sdk:${NETCORE_VERSION}-alpine
ENV ASPNETCORE_ENVIRONMENT=development
RUN addgroup -g 1000 dotnet \
    && adduser -u 1000 -G dotnet -s /bin/sh -D dotnet
RUN apk update \
  && apk --no-cache add curl procps unzip \
  && wget -qO- https://aka.ms/getvsdbgsh | /bin/sh /dev/stdin -v latest -l /vsdbg
USER dotnet
WORKDIR /home/dotnet
ENTRYPOINT [ "dotnet" ]
