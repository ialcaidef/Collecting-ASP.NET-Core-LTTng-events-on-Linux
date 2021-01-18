# Collecting-ASP.NET-Core-LTTng-events-on-Linux

#### Demonstration Steps

1. Open the command prompt.

2. To change directory to the **DemoFiles** project, run the following command:

   ```bash
    cd [Repository Root]\Allfiles\Mod08\Demofiles
   ```

3. To create a new **WebApi** project, run the following command:

   ```bash
    dotnet new webapi -n MonitorLTTng    
   ```

4. To change directory to the **MonitorLTTng** project, run the following command:

   ```bash
    cd MonitorLTTng
   ```

5. To open the project in Visual Studio Code, run the following command:

   ```bash
    code .
   ```

6. In Visual Studio Code, add a new Dockerfile (without file extension) to the root directory.

7. To the Dockerfile, add the following commands:

   ```sh
   FROM microsoft/dotnet:sdk AS build-env
   WORKDIR /app
   
   # Copy csproj and restore as distinct layers
   COPY *.csproj ./
   RUN dotnet restore
   
   # Copy everything else and build
   COPY . ./
   RUN dotnet publish -c Release -o out
   
   # Build runtime image
   FROM microsoft/dotnet:aspnetcore-runtime
   WORKDIR /app
   COPY --from=build-env /app/out .
   # Enviroment variable
   ENV COMPlus_EnableEventLog 1
   ENTRYPOINT ["dotnet", "MonitorLTTng.dll"]
   ```

8. Switch to the command prompt.

9. To build the docker image, run the following command:

   ```bash
   docker build -t monitor .
   ```

10. To run a new container with the **monitor** image, run the following command:

    ```
    docker run -d -p 8080:80 --name myapp monitor
    ```

11. To get a shell in the container, run the following command:

    ```
    docker exec -it myapp bash
    ```

12. To update packages in the container, run the following command:

    ```bash
    apt update
    ```

13. To install **software-properties-common**, run the following command:

    ```bash
    apt-get install software-properties-common
    ```

14. To update the list of packages, run the following command:

    ```bash
    apt-get update
    ```

15. To install the main **LTTng** packages, run the following command:

    ```bash
    apt-get install lttng-tools lttng-modules-dkms liblttng-ust-dev
    ```

16. To create a new **LTTng** session, run the following command:

    ```bash
    lttng create exceptions-trace
    ```

17. To add context data (process id, thread id, process name) to each event, run the following command:

    ```bash
    lttng add-context --userspace --type vpid
    lttng add-context --userspace --type vtid
    lttng add-context --userspace --type procname
    ```

18. To create an event rule to record all the events starting with **DotNETRuntime**, run the following command:

    ```bash
    lttng enable-event --userspace --tracepoint DotNETRuntime:*
    ```

19. To start recording events, run the following command:

    ```bash
    lttng start
    ```

20. Open a browser and go to the following URL:

    ```url
    http://localhost:8080/api/values
    ```

21. Refresh the page a few times.

22. Switch to the command prompt.

23. To stop the recording, run the following command:

    ```bash
    lttng stop
    ```

24. To **destroy** the session, run the following command:

    ```bash
    lttng destroy
    ```

25. To see all the recorded events, paste the following command, press Tab, and then press Enter:

    ```bash
    babeltrace /root/lttng-traces/exceptions-trace
    ```

26. Explore all the events.

27. To exit from bash, run the following command:

    ```bash
    exit
    ```

28. To kill the running container, run the following command:

    ```bash
    docker kill myapp
    ```

29. To remove the container, run the following command:

    ```bash
    docker rm myapp
    ```

30. Close all the windows.

![imagen](img1.png)
