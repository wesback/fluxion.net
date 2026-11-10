# Build context: repository root (see docker-compose.yml and .github/workflows/docker.yml)
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /repo

# Restore first so the layer is cached until a project file changes
COPY global.json Directory.Build.props .editorconfig ./
COPY src/Fluxion.Domain/*.csproj src/Fluxion.Domain/
COPY src/Fluxion.Application/*.csproj src/Fluxion.Application/
COPY src/Fluxion.Infrastructure/*.csproj src/Fluxion.Infrastructure/
COPY src/Fluxion.Api/*.csproj src/Fluxion.Api/
RUN dotnet restore src/Fluxion.Api/Fluxion.Api.csproj

COPY src/ src/
RUN dotnet publish src/Fluxion.Api/Fluxion.Api.csproj \
    --configuration Release --no-restore --output /app \
    -p:OpenApiGenerateDocuments=false

FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS runtime
WORKDIR /app
COPY --from=build /app .
# Non-root user built into the official .NET images
USER $APP_UID
ENV ASPNETCORE_HTTP_PORTS=8080
EXPOSE 8080
ENTRYPOINT ["dotnet", "Fluxion.Api.dll"]
