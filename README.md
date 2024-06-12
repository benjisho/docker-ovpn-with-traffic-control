# OpenVPN and Traffic Controller Server Setup

This repository contains a Docker Compose setup for running two OpenVPN client containers and a Traffic Control (TC) server. The `tc-server` container monitors and controls the traffic of the `openvpn_client_with_tc` container.

## Prerequisites

Before starting, ensure you have the following installed on your host machine:

- Docker
- Docker Compose

## Directory Structure

Make sure your directory structure looks like this:

```
.

└── static
    └── pure-min.css
└── templates
    └── main.html
└── vpn
    ├── openvpn_client_no_tc.ovpn
    └── openvpn_client_with_tc.ovpn
├── Dockerfile_TC
├── Dockerfile_openvpn_client
├── docker-compose.yml
├── main.py
└── openvpn.sh
```

## Configuration

### Network Configuration

The Docker Compose file defines three networks:
- `vpn_net`: A macvlan network for VPN traffic.
- `no_tc_net`: A bridge network for the `openvpn_client_no_tc` container.
- `tc_net`: A bridge network for the `openvpn_client_with_tc` container.

Make sure to replace `enp8s0` in `vpn_net` with your actual **inbound** network interface.

### IP Configuration

Ensure the specified IP addresses are unique and not in use:
- `openvpn_client_no_tc`:
    - `10.9.91.200` on `no_tc_net`
    - and `10.9.96.101` on `vpn_net`.
- `openvpn_client_with_tc`:
    - `10.9.92.200` on `tc_net`
    - and `10.9.96.102` on `vpn_net`.

> In the `command:` section of each container:
> Replace `host-outbound-physical-interface-ip` with the actual IP address of your host's outbound interface.

## Setup Instructions

1. **Install Docker on Your Debian Machine**

    Use this [link](https://docs.docker.com/engine/install/) to install Docker.

2. **Build the Docker Images**

   Build the Docker images for the TC server and OpenVPN clients:
   ```sh
   docker compose build
   ```

3. **Start the Containers**

   Start the containers using Docker Compose:
   ```sh
   docker compose up -d
   ```

   This command will create and start the containers in detached mode.

    > Or just `docker compose up -d --build`
 
4. **Verify the Setup**

   Check the status of the running containers:
   ```sh
   docker compose ps
   ```

   Check the status of the OpenVPN tunnels:
   ```sh
   docker compose logs -f 
   ```

5. **Access the TC Server Web Interface**

   The TC server web interface will be accessible at:
   ```
   http://<openvpn_client_with_tc_ip>:5000
   ```

   Replace `<openvpn_client_with_tc_ip>` with the IP address assigned to the `openvpn_client_with_tc` container.

## Monitoring Network Traffic

To monitor network traffic on UDP port 51353 using `tcpdump`, run the following command on the host machine:
```sh
sudo tcpdump -i any -n udp port 51353
```

## Stopping the Containers

To stop the running containers, use the following command:
```sh
docker compose down
```

## Delete the entire docker compose setup (Docker networks and images)

To remove everything that Docker Compose has set up, use the following command:
```sh
docker system prune -a
```

## Additional Information

- Ensure that your OpenVPN configuration files (`.ovpn`) are correctly set up and placed in the `vpn` directory.
- Adjust environment variables and network configurations as needed to match your specific setup and requirements.

## Troubleshooting

If you encounter any issues, check the logs of the containers using:
```sh
docker compose logs -f
```

For more detailed information, you can inspect individual container logs:
```sh
docker compose logs -f <container_name>
```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.