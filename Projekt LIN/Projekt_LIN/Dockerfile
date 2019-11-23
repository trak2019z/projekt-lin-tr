FROM mcr.microsoft.com/dotnet/core/aspnet:3.0-buster-slim AS base

RUN apt-get update
RUN apt-get install -y curl ca-certificates nano openssl
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt install -y nodejs
RUN npm -v
#WORKDIR /app

FROM mcr.microsoft.com/dotnet/core/sdk:3.0-buster AS build
WORKDIR /src
COPY ["Projekt LIN/Projekt_LIN/Projekt_LIN.csproj", "Projekt LIN/Projekt_LIN/"]
COPY ["Projekt LIN/DAL/DAL.csproj", "Projekt LIN/DAL/"]

RUN dotnet restore "Projekt LIN/Projekt_LIN/Projekt_LIN.csproj"
COPY . .

WORKDIR "/src/Projekt LIN/Projekt_LIN"
RUN apt-get update
RUN apt-get install -y curl 
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt install -y nodejs
RUN npm -v
WORKDIR "/src/Projekt LIN/Projekt_LIN/ClientApp"

WORKDIR "/src/Projekt LIN/Projekt_LIN"
RUN dotnet build "Projekt_LIN.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Projekt_LIN.csproj" -c Release -o /app/publish

WORKDIR /src

FROM base AS final

WORKDIR /app
COPY --from=publish /app/publish .

## Add Certificate
COPY ssl-selfsigned.crt /usr/local/share/ca-certificates/
RUN update-ca-certificates
COPY ssl.pfx .

CMD ["dotnet", "Projekt_LIN.dll"]
