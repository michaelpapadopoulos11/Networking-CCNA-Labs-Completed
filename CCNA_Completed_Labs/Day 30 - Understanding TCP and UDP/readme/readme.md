## Day 30 - TCP/UDP, Wireshark Demo

In this lab I was tasked to install Wireshark, a packet capturing software, and record my some network activity on my computer. The purpose of this lab is to understand TCP and UDP process in the real-world.

After installing Wireshark, I began by recording a capture of my PCs network. In this capture I simply:

1. Opened Google Chrome
2. Typed "youtube.com" in the search bar
3. Typed a video into YouTubes search bar
4. Watched a video for around 30 seconds and stopped the recording.

After the capture was completed Wireshark recorded **5,304 Packets**.

<p align="center">
  <img width="855" height="573" alt="d30-step1" src="https://github.com/user-attachments/assets/c765e092-aa9e-4795-bbc3-c730e99adcf6" />
</p>

## Find a TCP three-way handshake (connection establishment).

By using `tcp.flags.syn == 1` we can find the origin of a three-way handshake in the recorded capture.

<p align="center">
  <img width="1501" height="944" alt="d30-step2" src="https://github.com/user-attachments/assets/14d4926b-48bf-466a-9a07-0ec35d97b373" />
</p>

The "relative sequence number" is set to 1, indicating this is the beginning of the SYN (Sychronise phase).

### The Handshake Sequence:
**1. Packet 238 [SYN] -** My PC (Source Port 57,071) sends a SYN request to the server (Destination Port 80).

**2. Packet 249 [SYN-ACK] -** The server acknoledges packet 238 and responds from itself, Source port 80, to the Destination port of the PC (Destination Port 57,071).

**3. Packet 250 [ACK] -** The PC recieved the acknoledgement from the server and the three-way handshake is completed!

<p align="center">
  <img width="45%" alt="d30-step3" src="https://github.com/user-attachments/assets/1f84e2eb-7054-47b3-a6b4-08d5fa10c805" />
  <img width="45%" alt="d30-syn-synack-ack" src="https://github.com/user-attachments/assets/b347cda7-7e3d-41f6-92eb-de913ddd1f33" />
</p>

The above image showcases the three-way handshake process in-action.
