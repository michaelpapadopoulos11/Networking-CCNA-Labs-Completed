## Day 30 - Understanding TCP/UDP with Wireshark

The goal of this lab was to use Wireshark to capture and analyse live network traffic on my PC, demonstrating how TCP and UDP protocols operate in a real-world environment.

After installing Wireshark, I began capturing my PCs network. In this capture I simply:

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
**1. Packet 238 [SYN] -** My PC (Source Port 57,071) sends a Synchronize request to the server (Destination Port 80). The relative sequence number is 0, marking the start of the three-way handshake between the PC and end-host server.

**2. Packet 249 [SYN-ACK] -** The server acknowledges packet 238 and responds from its own Source Port 80 to the Destination Port of the PC (Port 57,071).

**3. Packet 250 [ACK] -** The PC received the acknowledgment from the server, and the three-way handshake is complete! The connection state is now Established.

<p align="center">
  <img width="45%" alt="d30-step3" src="https://github.com/user-attachments/assets/1f84e2eb-7054-47b3-a6b4-08d5fa10c805" />
  <img width="45%" alt="d30-syn-synack-ack" src="https://github.com/user-attachments/assets/b347cda7-7e3d-41f6-92eb-de913ddd1f33" />
</p>

The above image showcases the three-way handshake process in-action.
