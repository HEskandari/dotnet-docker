FROM arm32v7/buildpack-deps:bionic-scm

# Install .NET CLI dependencies
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        libc6 \
        libgcc1 \
        libgssapi-krb5-2 \
        libicu60 \
        liblttng-ust0 \
        libssl1.0.0 \
        libstdc++6 \
        zlib1g \
    && rm -rf /var/lib/apt/lists/*

# Install .NET Core SDK
ENV DOTNET_SDK_VERSION 2.1.810

RUN curl -SL --output dotnet.tar.gz https://dotnetcli.azureedge.net/dotnet/Sdk/$DOTNET_SDK_VERSION/dotnet-sdk-$DOTNET_SDK_VERSION-linux-arm.tar.gz \
    && dotnet_sha512='fe1ec616d88f2d9b9da4a9f48ede9ec2937e15f7d129907e8f14931e91d297d634689f61877df33efd39141f71f53747c55711d0361de04932ad604afe1bff41' \
    && echo "$dotnet_sha512 dotnet.tar.gz" | sha512sum -c - \
    && mkdir -p /usr/share/dotnet \
    && tar -zxf dotnet.tar.gz -C /usr/share/dotnet \
    && rm dotnet.tar.gz \
    && ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet \
    # Add NuGet cache (ARM SDK doesn't include it)
    && curl -SL --output /usr/share/dotnet/sdk/$DOTNET_SDK_VERSION/nuGetPackagesArchive.lzma https://dotnetcli.azureedge.net/dotnet/Sdk/$DOTNET_SDK_VERSION/nuGetPackagesArchive.lzma \
    && lzma_sha512='c1362568e1fcdada44de0c8806cdd47875b869df76235f83031386819354a8f4d8597077e7c7f401b09bb64233c0b0380e6d33b90b650256ea21a9e77e2730f8' \
    && echo "$lzma_sha512 /usr/share/dotnet/sdk/$DOTNET_SDK_VERSION/nuGetPackagesArchive.lzma" | sha512sum -c -

ENV \
    # Configure web servers to bind to port 80 when present
    ASPNETCORE_URLS=http://+:80 \
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true \
    # Enable correct mode for dotnet watch (only mode supported in a container)
    DOTNET_USE_POLLING_FILE_WATCHER=true \
    # Skip extraction of XML docs - generally not useful within an image/container - helps performance
    NUGET_XMLDOC_MODE=skip

# Trigger first run experience by running arbitrary cmd to populate local package cache
RUN dotnet help
