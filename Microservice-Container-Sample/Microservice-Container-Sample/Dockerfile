FROM mcr.microsoft.com/dotnet/sdk:5.0-alpine AS publish
WORKDIR /src
COPY ["Microservice-Container-Sample/Microservice-Container-Sample.csproj", "Microservice-Container-Sample/"]

RUN dotnet restore "Microservice-Container-Sample/Microservice-Container-Sample.csproj" --runtime alpine-x64
COPY . .
WORKDIR "/src/Microservice-Container-Sample"
RUN dotnet publish "Microservice-Container-Sample.csproj" -c Release -o /app/publish \
  --no-restore \
  --runtime alpine-x64 \
  --self-contained true \
  /p:PublishTrimmed=true \
  /p:PublishSingleFile=true

FROM mcr.microsoft.com/dotnet/runtime-deps:5.0-alpine AS final

RUN adduser --disabled-password \
  --home /app \
  --gecos '' dotnetuser && chown -R dotnetuser /app

# upgrade musl to remove potential vulnerability
RUN apk upgrade musl

USER dotnetuser
WORKDIR /app
EXPOSE 80
COPY --from=publish /app/publish .

ENTRYPOINT ["./Microservice-Container-Sample"]