# Docker configurations
1. Create the project
```sh
dotnet new webapp --auth Individual -o <project_name>
```
2. Install the needed tools and packages
```sh
dotnet tool install --global dotnet-ef
dotnet add package Pomelo.EntityFrameworkCore.MySql
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
```
3. Specify execution domain (right after the `app.Run();` command)
```cs
app.Urls.Add("http://0.0.0.0:80");
```
4. Install the Docker images for each tool that requires installation
```sh
docker pull mcr.microsoft.com/dotnet/aspnet:8.0
docker pull mysql
```
4. Add the Dockerfile
```Dockerfile
# Hot reload enabled Dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS base
WORKDIR /app
EXPOSE 80
# Copy project files
COPY . .
# Restore dependencies
RUN dotnet restore
# Set environment variables for hot reloading
ENV ASPNETCORE_ENVIRONMENT=Development
ENV DOTNET_USE_POLLING_FILE_WATCHER=true
# Run the application with hot reloading
ENTRYPOINT ["dotnet", "watch", "run", "--no-launch-profile"]
```
6. Build the Docker image
```sh
docker build -t <image_name> .
```
7. Create Docker network
```sh
docker network create <network_name>
```
8. Run the image
```sh
docker run -d -p 8080:80 -v "$(pwd)":/app -v /app/bin -v /app/obj --network aspnetwork --name <container_name> <prev_image_name>
```
9. Run the database container
```sh
docker run -d --name <container_name> --network <network_name> -e MYSQL_ROOT_PASSWORD=<root_pass> -e MYSQL_DATABASE=<db_name> -e MYSQL_USER=<user_name> -e MYSQL_PASSWORD=<user_pass> -p 3306:3306 mysql:8.0.40
```
10. Migrations issues
```sh
dotnet new tool-manifest
dotnet tool install dotnet-ef
```



