FROM node:10-alpine as build-node
WORKDIR /ClientApp
COPY ClientApp/package.json .
COPY ClientApp/package-lock.json .
RUN npm install
COPY ClientApp/ . 
RUN npm run build

FROM mcr.microsoft.com/dotnet/core/sdk:2.1 as build-net
# RUN apt update && \
#     apt install unzip procps && \
#     curl -sSL https://aka.ms/getvsdbgsh | /bin/sh /dev/stdin -v latest -l /vsdbg
ENV BuildingDocker true
ENV ASPNETCORE_ENVIRONMENT=Development

WORKDIR /app
COPY *.csproj .
RUN dotnet restore
COPY . .
RUN dotnet build
RUN dotnet publish -o /ttweb
WORKDIR /

FROM mcr.microsoft.com/dotnet/core/aspnet:2.1 as runtime
RUN apt update && \
    apt install unzip procps -y && \
    curl -sSL https://aka.ms/getvsdbgsh | /bin/sh /dev/stdin -v latest -l /vsdbg
WORKDIR /web
COPY --from=build-net /ttweb .
COPY --from=build-node /ClientApp/build ./ClientApp/build
ENTRYPOINT [ "dotnet","Tailwind.Traders.Web.dll" ]
