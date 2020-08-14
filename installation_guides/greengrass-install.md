# Install Greengrass Core Software on Ubuntu

1. Follow steps in [Configure AWS IoT Greengrass on AWS IoT](https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-config.html) to create a group and register the core. Download the security resources package and copy this to the Greengrass core device.

1. Install the Java 8 runtime. This is required for the Greengrass stream manager and the SiteWise connector.

    ```bash
    sudo apt install openjdk-8-jdk
    ```

1. Create symbolic link from Java installation to the Java 8 directory.

    ```bash
    sudo ln -s /usr/bin/java /usr/bin/java8
    ```

1. Add the greengrass user and usergroup.

    ```bash
    sudo adduser --system ggc_user
    sudo addgroup --system ggc_group
    ```

1. Download the latest Greengrass core software. As of writing this was 1.10.2.

    ```bash
    wget https://d1onfpft10uf5o.cloudfront.net/greengrass-core/downloads/1.10.2/greengrass-linux-x86-64-1.10.2.tar.gz
    ```

1. Install the Greengrass Core software and the security resources.

    ```bash
    sudo tar -xzvf  greengrass-linux-x86-64-1.10.2.tar.gz -C /
    sudo tar -xzvf {10-digit hash}-setup.tar.gz -C /greengrass
    ```

1. Download the root CA certificate.

    ```bash
    sudo wget -O /greengrass/certs/root.ca.pem https://www.amazontrust.com/repository/AmazonRootCA1.pem
    ```

1. Start the AWS IoT Greengrass service on the device.

    ```bash
    cd /greengrass/ggc/core/
    sudo ./greengrassd start
    ```

1. Register Greengrass as a service, create a systemd service file.

    ```bash
    sudo nano /etc/systemd/system/greengrass.service
    ```

    And populate it with the following:

    ```bash
    [Unit]
    Description=Greengrass Daemon

    [Service]
    Type=forking
    PIDFile=/var/run/greengrassd.pid
    Restart=on-failure
    ExecStart=/greengrass/ggc/core/greengrassd start
    ExecReload=/greengrass/ggc/core/greengrassd restart
    ExecStop=/greengrass/ggc/core/greengrassd stop

    [Install]
    WantedBy=multi-user.target
    ```

    *n.b. In `nano` press ``Ctrl`` + `O` to save your changes followed by ``Ctrl`` + ``X`` to exit.*

1. Start the Greengrass daemon and check it's status.

    ```bash
    sudo systemctl start greengrass.service
    sudo systemctl status greengrass.service
    ```

1. Enable the Greengrass daemon on boot.

    ```bash
    sudo systemctl enable greengrass.service
    ```


## Install SiteWise connector on Greengrass enabled device

1. Create the SiteWise directory and give the `ggc_user` permissions.
    ```bash
    sudo mkdir /var/sitewise 
    sudo chown ggc_user /var/sitewise
    sudo chmod 700 /var/sitewise
    ```

1. open port 8883 for MQTT communication

    ```bash
    sudo ufw allow 8883/tcp
    ```

    *additional information regarding ports can be found [here](https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-core.html#alpn-network-proxy)*

