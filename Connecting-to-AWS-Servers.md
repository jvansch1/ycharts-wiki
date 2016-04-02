# Connecting to Other Servers

In order to SSH into other servers that aren't defined in the SSH config, you will need to get the **Internal IP**
of the server you want to connect to, as well as the IP address of the NAT machine for the desired environment,
then run the following command:

```bash
ssh -o ProxyCommand='ssh ec2-user@<nat-ip> -W %h:%p' <internal-ip>
# Currently for staging:
ssh -o ProxyCommand='ssh ec2-user@52.6.96.159 -W %h:%p' <internal-ip>
# Currently for production:
ssh -o ProxyCommand='ssh ec2-user@52.4.31.32 -W %h:%p' <internal-ip>
```