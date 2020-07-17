### Prerequisites 

Your manager should have provided you with the following pieces of information in a secure manner. If you do not have all of them please let your manager know before proceeding:
1. **Server Address:** This is the IP of the VPN (i.e. `XXX.XXX.XXX.XXX`)
2. **Account Name:** This is your YCharts email
3. **Account Password:** This password should have been emailed to your ycharts email.
4. **Shared Secret:** This is a shared secret for access to the account. Your manager should provide this.

## Steps

1. Open `Systems Preferences > Network` and click the `+` icon in the bottom left.
    ![](https://i.imgur.com/UHiJJhX.png)
2. Make the following selections and click "Create". They need to be exact:
    - **Interface:** VPN
    - **VPN Type:** L2TP over IPSec
    - **Service Name:** YCharts 
    ![](https://i.imgur.com/qpFJH0d.png)
3. Fill in the following details:
    - **Configuration:** Default
    - **Server Address:** XXX.XXX.XXX.XXX (This should have been provided to you before starting)
    - **Account Name:** XXX@ycharts.com (This should have been provided to you before starting)
    ![](https://i.imgur.com/vR5TYk0.png)
4. Click "Authentication Settings..." and enter your "Password" and "Shared Secret". Then click "OK".
    ![](https://i.imgur.com/5EK4Ho9.png)
5. Click "Advanced..." and make sure you click "Send all traffic over VPN connection"
    ![](https://i.imgur.com/VKXPQel.png)
6. Now click "Connect" and it should show "Status: Connected". To test that you are in fact on the VPN go to [staging.ycharts.com](https://staging.ycharts.com/) which is only accessible via the VPN.
    > NOTE: If you are physically in the NYC office you will not be able to test the connection since the VPN is our literal VPN location. So you must test this from home or outside the office to ensure it is working. Please make a point to do this in your first week. If it doesn't work please let your manager know.

    ![](https://i.imgur.com/ORggqAg.png)

