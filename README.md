# Concurrent File Sharing Using TCP Sockets with Mininet and SDN

## Project Overview

This project demonstrates a TCP client-server file-sharing application running over a Software-Defined Networking (SDN) topology emulated with Mininet.

The topology contains one server host (H1), two client hosts (H2 and H3), and an Open vSwitch (s1). An OS-Ken controller communicates with the switch using OpenFlow 1.3.

The server accepts multiple client connections using a separate thread for each connected client. Authenticated clients can list files available on the server, download files, and upload files.

## Team Members

- Prasanna S — PES1UG25CS383
- Pranshu Chaturvedi — PES1UG25CS380
- Prateek Kumar Panda — PES1UG25CS384

**Institution:** PES University  
**Project Guide:** Teja L.

## Main Features

- TCP server listens on port 5000.
- Server accepts multiple client connections using Python threads.
- Client authentication is required before file operations.
- Clients can list files stored on the server.
- Clients can download files from the server.
- Clients can upload files to the server.
- File transfers use a bounded buffer and a maximum file size of 100 MB.
- Uploaded-file metadata is recorded in file_metadata.json.
- Mininet emulates the hosts and switch.
- OS-Ken provides the OpenFlow 1.3 controller application.

## Technologies Used

- Python 3
- TCP Sockets
- Mininet
- Open vSwitch
- OpenFlow 1.3
- OS-Ken
- Ubuntu Linux

## Project Structure

    CN_MINI_PROJECT/
    ├── README.md
    ├── requirements.txt
    ├── server1.py
    ├── client_h2.py
    ├── client_h3.py
    ├── sdn_controller.py
    ├── file_metadata.json
    └── files/
        ├── 1.jpg
        ├── 2.jpg
        ├── 3.jpg
        ├── 4.jpg
        └── test20MB.bin

## Network Topology

| Node | IP Address | Role |
|------|------------|------|
| H1 | 10.0.0.1 | TCP File Server |
| H2 | 10.0.0.2 | Client 1 |
| H3 | 10.0.0.3 | Client 2 |
| s1 | — | Open vSwitch |
| OS-Ken | 127.0.0.1:6633 | SDN Controller |

The TCP server listens on port 5000.

## Requirements

The Python dependency used by the controller is:

    os-ken==3.1.1

Mininet and Open vSwitch must also be installed.

Install the Python dependency:

    python3 -m pip install -r requirements.txt

If using the existing OS-Ken virtual environment:

    source ~/osken-legacy-env/bin/activate
    python3 -m pip install -r requirements.txt

## Running the Project

Use separate terminal windows for the controller and Mininet. The server and clients must run inside their respective Mininet host namespaces.

### 1. Start the SDN Controller

    cd /media/sf_CN_MINI_PROJECT
    source ~/osken-legacy-env/bin/activate
    osken-manager sdn_controller.py

### 2. Start Mininet

    sudo mn -c

    sudo mn --topo single,3 --switch ovs,protocols=OpenFlow13 --controller remote,ip=127.0.0.1,port=6633

At the Mininet prompt, test connectivity:

    pingall

### 3. Start the Server on H1

    h1 python3 /media/sf_CN_MINI_PROJECT/server1.py

Keep the server running.

### 4. Start the H2 Client

Run the following command inside H2's Mininet namespace:

    python3 /media/sf_CN_MINI_PROJECT/client_h2.py

### 5. Start the H3 Client

Run the following command inside H3's Mininet namespace:

    python3 /media/sf_CN_MINI_PROJECT/client_h3.py

### 6. Client Menu

After successful authentication, the menu provides:

1. List files
2. Download a file
3. Upload a file
4. Logout and exit

### 7. Inspect OpenFlow Rules

At the Mininet prompt:

    sh ovs-ofctl -O OpenFlow13 dump-flows s1

## Application Protocol

The client and server exchange newline-terminated command headers.

- AUTH username password — authenticate a client.
- LIST — list available files.
- DOWNLOAD filename — download a file.
- UPLOAD filename size — upload a file.

File payloads are transferred as raw bytes after the corresponding header.

## Testing Checklist

- Start the OS-Ken controller.
- Start Mininet with OpenFlow 1.3.
- Run pingall and check connectivity.
- Start the server on H1.
- Log in from H2 and list files.
- Download a test file from H2.
- Log in from H3 and repeat the download test.
- Test uploading a file.
- Test overlapping H2 and H3 transfers.
- Compare file sizes and SHA-256 hashes.

Only report tests that have actually been performed. Mininet throughput results are from a virtualized environment and are not physical-network throughput measurements.

## Security Notes

The supplied server source contains a hard-coded demonstration credential. Replace it before publishing the repository. The current protocol sends credentials over ordinary TCP without encryption. Use TLS and proper authentication design before real-world use.

## Troubleshooting

- Client cannot connect: Check that the server is running on H1 and that the client uses 10.0.0.1:5000.
- Controller does not connect: Check OS-Ken and the OpenFlow 1.3 configuration.
- Download directory error: Create the configured H2/H3 download directories.
- File not found: List the server files and enter the exact filename.
- Port already in use: Stop the old server process before restarting it.

## Acknowledgement

This project was developed as a Computer Networks mini-project at PES University.
