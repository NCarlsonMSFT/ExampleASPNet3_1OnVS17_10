#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

# This will only be set when running from VS
ARG MY_LAUNCHING_FROM_VS
# This sets the base runtime image, but only if MY_LAUNCHING_FROM_VS has been defined
ARG BASE_RUNTIME_IMAGE=${MY_LAUNCHING_FROM_VS:+aspnet6_0and3_1}

# The base image when not launching from VS
FROM mcr.microsoft.com/dotnet/aspnet:3.1 AS aspnet3_1
# A stage for copying the 6.0 runtime from
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS aspnet6_0
# When launching from VS, copy the runtime from a 6.0 image (as the VS tools need at least 6.0) onto the 3.1 image
FROM aspnet3_1 AS aspnet6_0and3_1
COPY --from=aspnet6_0 /usr/share/dotnet/host /usr/share/dotnet/host
COPY --from=aspnet6_0 /usr/share/dotnet/shared /usr/share/dotnet/shared

# The from defaults to aspnet3_1, but can be overridden above
FROM ${BASE_RUNTIME_IMAGE:-aspnet3_1} as base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:3.1 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["ExampleASPNet3_1OnVS17_10/ExampleASPNet3_1OnVS17_10.csproj", "ExampleASPNet3_1OnVS17_10/"]
RUN dotnet restore "./ExampleASPNet3_1OnVS17_10/ExampleASPNet3_1OnVS17_10.csproj"
COPY . .
WORKDIR "/src/ExampleASPNet3_1OnVS17_10"
RUN dotnet build "./ExampleASPNet3_1OnVS17_10.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./ExampleASPNet3_1OnVS17_10.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "ExampleASPNet3_1OnVS17_10.dll"]