Issues We Have Seen with Chicago Internet

1. A user has spyware that is downloading the entire traffic.
2. The switch is kind of failure prone. If someone plugs a port back into another port of the switch, it will bring down the entire network. NOT A JOKE! If you want to know if it's a switch problem, TAKE OUT THE SWITCH <> ROUTER. Have a computer plug directly into the router/firewall. If your internet works when that happens, it's the SWITCH!!!!!!!
3. ATT Modem flips into bridge modes and completely fails to work.
The static IPs are 
```
162.197.252.41 (statically assigned)
Gateway: 162.197.252.46; DNS: 8.8.8.8, 8.8.4.4
```

4. Comcast sometimes will just fail, you will then need to reset. This should actually be static, but Comcast fucked us, so this is done dynamically.
```
73.247.76.130 (via DHCP)
Gateway: 73.247.76.1; DNS: 75.75.75.75, 75.75.76.76
```

ATT / Comcast is set as a FAIL OVER solution. That is, if it ATT fails, it will switch over to Comcast! IE. For 99% of the time, Comcast is NOT used.

On the Meraki interface, you can see where the traffic is going ... either Internet 1 or Internet 2

If you need to login to the ATT modem .... here are the credentials
```
ATT Modem : login/password
```