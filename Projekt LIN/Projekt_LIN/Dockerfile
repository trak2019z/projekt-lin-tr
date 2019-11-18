FROM mcr.microsoft.com/dotnet/core/aspnet:3.0-buster-slim AS base

# NODE
# ENV NODE_VERSION 12.13.0
# ENV NODE_DOWNLOAD_URL https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.gz
# https://nodejs.org/download/release/v8.11.3/SHASUMS256.txt
# ENV NODE_DOWNLOAD_SHA 49a7374670a111b033ce16611b20fd1aafd3296bbc662b184fe8fb26a29c22cc

#RUN curl -SL "$NODE_DOWNLOAD_URL" --output nodejs.tar.gz
    ##&& echo "$NODE_DOWNLOAD_SHA nodejs.tar.gz" | sha256sum -c - \
#RUN tar -xzf "nodejs.tar.gz" -C /usr/local --strip-components=1
#RUN rm nodejs.tar.gz 
#RUN ln -s /usr/local/bin/node /usr/local/bin/nodejs
# END NODE

WORKDIR /app
EXPOSE 6780
EXPOSE 44367

RUN apt-get update
RUN apt-get install -y npm ca-certificates nano openssl

RUN npm install -g npm@6.9
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
RUN apt-get install -y npm 

RUN npm install -g npm@6.9
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
