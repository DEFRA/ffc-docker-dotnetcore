# Allow parent image version and registry to be set at build time
ARG PARENT_VERSION=1.0.0
ARG PARENT_REGISTRY=562955126301.dkr.ecr.eu-west-2.amazonaws.com

# Development stage, used the build the app and provide testing tools
FROM $PARENT_REGISTRY/ffc-dotnet-sdk:$PARENT_VERSION AS development

ARG PARENT_VERSION
ARG PARENT_REGISTRY
ARG PORT=3007

# Label the development image with the parent image it extends
LABEL uk.gov.defra.ffc.parent-image=${PARENT_REGISTRY}/ffc-node-development:${PARENT_VERSION}

# Create directories for app and test files to be copied into
RUN mkdir -p /home/dotnet/exampleService/ /home/dotnet/exampleService.Tests/

# Install test dependencies
COPY --chown=dotnet:dotnet ./exampleService.Tests/*.csproj ./exampleService.Tests/
RUN dotnet restore ./exampleService.Tests/exampleService.Tests.csproj

# Install app dependencies
COPY --chown=dotnet:dotnet ./exampleService/*.csproj ./exampleService/
RUN dotnet restore ./exampleService/exampleService.csproj

# Copy in test and app code
COPY --chown=dotnet:dotnet ./exampleService.Tests/ ./exampleService.Tests/
COPY --chown=dotnet:dotnet ./exampleService/ ./exampleService/

# Build the app
RUN dotnet publish ./exampleService/ -c Release -o /home/dotnet/out

# Set the default command for the development image to run a file watcher
CMD [ "watch", "--project", "./exampleService", "run", "--urls", "http://*:${PORT}" ]

# Production stage
FROM $PARENT_REGISTRY/ffc-dotnet-runtime:$PARENT_VERSION AS production

ARG PARENT_VERSION
ARG PARENT_REGISTRY
ARG PORT=3007

# Label the production image with the parent image it extends
LABEL uk.gov.defra.ffc.parent-image=${PARENT_REGISTRY}/ffc-node-development:${PARENT_VERSION}

# Expose the app on a fixed port
ENV ASPNETCORE_URLS=http://*:${PORT}
EXPOSE ${PORT}

# Copy the compiled app from the development build stage
COPY --from=development /home/dotnet/out/ ./

# Set the default command for the production image to run the compiled application
CMD [ "exampleService.dll" ]
