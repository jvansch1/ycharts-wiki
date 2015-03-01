## SonicWall Router Notes

## Redirecting Internet Traffic to Local Computer

The following steps describe the procedure to redirect traffic sent to the public IP
address of the router to a local IP address of a computer on the network.

The steps are explained pretty well in [this post.](http://www.firewalls.com/blog/sonicwall-open-ports/)
but the steps below explain in more detail or are more specific.

1. Log in to the [router admin.](http://10.0.0.1)

1. Open the **Wizards** menu located in the top right corner and start
   the **Public Server Wizard**.

1. On the first page, it prompts you to select a service. Choose **Other**
   and create a new service. Name it whatever you like but be descriptive!
   The port that you choose for the service will be the port that forwards
   requests to the internal server.

1. On the second page of the wizard, it prompts you to select a server.
   Name the server and give it the internal IP address of the computer
   you want requests forwarded to.

1. For the public IP address, the default is the default public IP address
   of the router and can be left as-is.

1. The wizard then gives a summary of all the stuff it's going to create
   and after applying, you should be good to go!

#### Superfeedr Example

I created a service **Test Superfeedr** on port 8800 and set up the server
with Zack's internal IP address. Thus, after applying the changes from the
wizard, all requests to http://98.116.12.24:8800 are forwarded to
http://10.0.0.???:8800.

This means it is required for Zack to run his devserver with

```bash
python manage.py runserver 0.0.0.0:8800
```
