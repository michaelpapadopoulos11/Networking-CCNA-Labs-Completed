# Day 4 - Basic Device Security Lab and Encryption

A short introductory lab focused on securing Cisco IOS devices. I learned the difference between the CLI **enable password** and the **enable secret** commands, how each is stored, and why the secret always overrides the password. The lab also covered basic configs such as changing hostname, copying/viewing running/startup-configs and enabling password encryption.

The `enable password` uses the weaker **Type 7 encryption** whereas the `enable secret` uses a more secure **MD5 hashing (Type 5)** and will always override the password. Type 7 encryption is considered weak because it can be easily reversed, while Type 5 hashes cannot be decrypted.

## Commands Used for this lab:
| **Command** | **Description** |
|--------------|----------------|
| `hostname R1` | Sets the device’s hostname to “R1”. |
| `enable password CCNA` | Configures an unencrypted password of "CCNA" for privileged EXEC mode. Stored as plain text in the running configuration. |
| `enable secret Cisco` | Configures an encrypted password of "Cisco" for privileged EXEC mode using MD5 hashing (Type 5 encryption). Overrides the `enable password`. |
| `service password-encryption` | Enables Type 7 encryption for all plain-text passwords in the configuration. |
| `show running-config` | Displays the devices current configuration, including encrypted passwords and other settings. |
| `copy running-config startup-config` | Copies the contents of `running-config` and saves it to the devices `startup-config`. |
| `exit` | Moves back one level in the command hierarchy (e.g., from global configuration to privileged EXEC mode). |
| `show version` | Displays system information such as IOS version, uptime, and hardware details. |

## Topology Screenshot
<img width="1393" height="876" alt="day 4 basic device security" src="https://github.com/user-attachments/assets/cfa6eaf3-ff54-4c3a-8fef-04abd0c9f594" />
