#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
ENV NODE_VERSION=18.12.0
RUN apt install -y curl
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
ENV NVM_DIR=/root/.nvm
RUN . "$NVM_DIR/nvm.sh" && nvm install ${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm use v${NODE_VERSION}
RUN . "$NVM_DIR/nvm.sh" && nvm alias default v${NODE_VERSION}
ENV PATH="/root/.nvm/versions/node/v${NODE_VERSION}/bin/:${PATH}"
RUN node --version
RUN npm --version
WORKDIR /src
COPY ["Calicot.WebApp/Calicot.WebApp.csproj", "Calicot.WebApp/"]
COPY ["Calicot.Shared/Calicot.Shared.csproj", "Calicot.Shared/"]
RUN dotnet restore "Calicot.WebApp/Calicot.WebApp.csproj"
COPY . .
WORKDIR "/src/Calicot.WebApp"
RUN dotnet build "Calicot.WebApp.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "Calicot.WebApp.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Calicot.WebApp.dll"]