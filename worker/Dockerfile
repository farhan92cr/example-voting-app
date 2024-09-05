# because of dotnet, we always build on amd64, and target platforms in cli
# dotnet doesn't support QEMU for building or running. 
# (errors common in arm/v7 32bit) https://github.com/dotnet/dotnet-docker/issues/1537
# https://hub.docker.com/_/microsoft-dotnet
# hadolint ignore=DL3029
# to build for a different platform than your host, use --platform=<platform>
# for example, if you were on Intel (amd64) and wanted to build for ARM, you would use:
# docker buildx build --platform "linux/arm64/v8" .
FROM --platform=${BUILDPLATFORM:-linux/amd64} mcr.microsoft.com/dotnet/sdk:7.0 as build

# Arguments for cross-platform builds
ARG TARGETPLATFORM
ARG TARGETARCH
ARG BUILDPLATFORM

# Echo the platforms for debugging
RUN echo "I am running on $BUILDPLATFORM, building for $TARGETPLATFORM"

# Set the working directory and copy project files
WORKDIR /source
COPY *.csproj .

# Restore dependencies
RUN dotnet restore -a ${TARGETARCH:-x64}

# Copy remaining files and publish the application
COPY . .
RUN dotnet publish -c release -o /app -a ${TARGETARCH:-x64} --self-contained false --no-restore

# Application image stage
FROM mcr.microsoft.com/dotnet/runtime:7.0
WORKDIR /app
COPY --from=build /app .

# Set the entrypoint for the application
ENTRYPOINT ["dotnet", "Worker.dll"]
