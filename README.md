# quil-manager
 Quilibrium Node deployment & management script for Quilibrium v1.4.19

## Table of Contents
- [Installation](#installation)
- [Usage](#usage)
- [Options & Features](#options--features)
- [Node performance evaluation](#node-performance-evaluation)

## Installation
 1. Download the `.sh` script file to the server.

 2. Under the directory where the script is located, run the following command to give the script execution permission.
```shell
chmod +x quil-manager.sh
```

## Usage
 1. Run the script with the following command.
```shell
./quil-manager.sh
```

 2. Select the options following the below steps:
    - Select `1` to Deploy Quilibrium Node (Only run once). Then run `source ~/.bashrc` to apply the changes.
    - (Compulsory) Upgrade the Quilibrium Node with `option 12` to 1.4.19.
    - Select `2` to verify the configurations and settings. Compare the output with the expected output in the [Options & Features](#options--features) section.
    - (Optional) Select `4` to Install qclient and grpcurl
    - Select `5` to start the Quilibrium Node and run for the first time to generate the .config folder.
    - (Optional) Select `3` to Modify the config.yml file.
    - (Optional) Reboot the server to apply the ufw changes with 'sudo reboot', then run the script to start the Quilibrium Node with `option 5`.
    - (Optional) When `REPAIR` is generated in the `.config` folder, sync the Quilibrium Node with `option 10`. **Nodes are earning rewards as long as they are running, even if they are not fully synced.**
    - (Optional) Backup the Quilibrium Node when `keys.yml` is fully generated (no longer showing `null`) with `option 11`.
    - (Optional) Limit the CPU usage with `option 13`.

## Options & Features
 1. **Deploy Node**: Deploy Quilibrium Node for new servers or re-deploy Quilibrium Node for existing servers. (Recommended to run only once)

 2. **Verify Configurations**: Verify the ufw settings and node configuration file. The output should show the following if the node is properly configured.

    Current UFW status:
    ```
    Status: active

    To                         Action      From
    --                         ------      ----
    22                         ALLOW       Anywhere                  
    8336                       ALLOW       Anywhere                  
    443                        ALLOW       Anywhere                  
    22 (v6)                    ALLOW       Anywhere (v6)             
    8336 (v6)                  ALLOW       Anywhere (v6)             
    443 (v6)                   ALLOW       Anywhere (v6) 
    ```            

    Current go version:
    ```
    go version go1.20.14 linux/amd64
    ```

    Current network settings:
    ```
    net.core.rmem_max = 600000000
    net.core.wmem_max = 600000000
    ```

    Current config.yml settings:
    ```
    listenGrpcMultiaddr: /ip4/127.0.0.1/tcp/8337
    statsMultiaddr: "/dns/stats.quilibrium.com/tcp/443"
    ```

    Current service configuration:
    ```
    [Unit]
    Description=Ceremony Client Go App Service
    [Service]
    Type=simple
    Restart=always
    RestartSec=5s
    WorkingDirectory=/root/ceremonyclient/node
    Environment=GOEXPERIMENT=arenas
    ExecStart=/root/ceremonyclient/node/node-1.4.18-linux-amd64
    [Install]
    WantedBy=multi-user.target
    ```

    Current qclient and grpcurl installation status:
    ```
    grpcurl  qclient
    ```

 3. **Modify config.yml**: After running the node for the first time, the config.yml file will be generated. This option will modify the `listenGrpcMultiaddr` and `statsMultiaddr` settings in the config.yml file to enable the GRPC.

 4. **Install qclient and grpcurl**: Install qclient and grpcurl for the Quilibrium Node.

 5. **Start Node**: Start the Quilibrium Node service.

 6. **Stop Node**: Stop the Quilibrium Node service.

 7. **View logs**: Display real-time logs of the Quilibrium Node. Press `Ctrl + C` to exit the log.

 8. **View Node status(GRPC)**: Display the status of the Quilibrium Node using GRPC. Install the `grpcurl` and modify the config.yml file before using this option.

 9. **Check Rewards**: Check the rewards earned after 1.4.19.

 10. **Sync Node**: Replace the `store` folder with the latest snapshot and create a backup of the current `store` folder.

 11. **Backup Node keys**: Create a backup of the `config.yml` and `keys.yml`. A scp command will be generated for you to copy the backup file to your local machine.

 12. **Upgrade Node**: Update the Quilibrium Node to the latest version.

 13. **Limit the CPU usage**: Enter `0-100` to limit the CPU usage of the Quilibrium Node to certain percentage. VPS providers may suspend the server if the CPU usage is too high.

## Node performance evaluation
 As of version 1.4.18, real-time rewards are not available. Instead, the contribution to the network is evaluated based on how quickly a node can sync, which is measured by the frame rate. **Faster frame rates indicate higher performance and contribution to the network**.

 As of the 5.31 reward update, the Post-1.4.18 rewards indicate that **nodes with higher frame rates accumulate more rewards than those with lower rates**. This shift from the previous evenly distributed rewards system emphasizes rewarding nodes for their performance and contribution to the network.

 According to data from various nodes (from various providers), VPS or dedicated servers with lower frame rates accumulate significantly fewer rewards, even if their CPU cores are higher than other servers. This emphasizes the importance of frame rates in the rewards calculation. While there is no conclusive evidence directly linking CPU cores to frame rates, this observation supports the assumption that frame rates play a crucial role in the rewards system.

 ### Check the Quilibrium Node frame rate
 This script will take snapshots of current max_frame and log the data in `~/frame_snapshot.txt`. The script will also calculate the average frame rate based on the first and last snapshots, and display the current and projected frame rates of the Quilibrium Node. 

 To check the current and projected frame rates of the Quilibrium Node, **make sure the node is running with GRPC enabled** and follow the steps below:
 1. Grant permission to the script.
```shell
chmod +x check_frame_rate.sh
```

 2. Run the script serveral times to get the average frame rate.
```shell
./check_frame_rate
```

 3. The script will display the current and projected frame rates of the Quilibrium Node. Example output:
```
PeerId: <peer-id>
-------------------------------------------------------
Current rate: 1.25 frames per second
Projected rate: 75.00 frames per minute
Projected rate: 4500.00 frames per hour
Projected rate: 108000.00 frames per day
-------------------------------------------------------
```
