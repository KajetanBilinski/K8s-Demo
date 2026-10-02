FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build

WORKDIR /src

COPY ["src/TaskApi/TaskApi.csproj", "src/TaskApi/"]
RUN dotnet restore "src/TaskApi/TaskApi.csproj"

COPY . .

WORKDIR "/src/src/TaskApi"

RUN dotnet publish "TaskApi.csproj" \
    -c Release \
    -o /app/publish \
    /p:UseAppHost=false


FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS final

WORKDIR /app

COPY --from=build /app/publish .

EXPOSE 8080

ENTRYPOINT ["dotnet", "TaskApi.dll"]