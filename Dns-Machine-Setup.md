### Setting Up a DNS Machine

## Mac Mini

### Install BIND

```bash
brew install bind
```

### Add Zones to named.conf
You will then need to edit the `named.conf` file installed by Homebrew, not the one native to OS X!
This is located at `/usr/local/etc/named.conf`. You will need to add the following two zones after
the `localhost` zone in this file:

```
zone "ycharts.local" {
    type master;
    file "ycharts.local.db";
};

zone "0.0.10.in-addr.arpa" {
    type master;
    file "rev.0.0.10.in-addr.arpa";
};
```

### Add zone files
You will then need to add the files for each of the zones. Homebrew `bind` expects zone files to
be located at `/usr/local/var/named/`.

#### /usr/local/var/named/ycharts.local.db

```
$TTL 3D
@ IN SOA ns.ycharts.local. admin.ycharts.local. (
        928295
        28800
        3600
        604800
        38400
);
ycharts.local.  IN  NS  ns.ycharts.local.
ns              IN  A   10.0.0.20
buffett         IN  A   10.0.0.20
austin          IN  A   10.0.0.92
james           IN  A   10.0.0.81
james-w         IN  A   10.0.1.81
ryan            IN  A   10.0.0.91
ara             IN  A   10.0.0.83
ara-w           IN  A   10.0.1.83
john            IN  A   10.0.0.70

```

#### /usr/local/var/named/rev.0.0.10.in-addr.arpa

```
$TTL 3D
$ORIGIN 0.0.10.in-addr.arpa.
@   IN  SOA     ns.ycharts.local.   root.ycharts.local. (
        9222997
        28800
        3600
        604800
        86400
);
    IN  NS  ns.ycharts.local.
20  IN  PTR ns.ycharts.local.
62  IN  PTR ie7.ycharts.local.
61  IN  PTR ie8.ycharts.local.
60  IN  PTR ie9.ycharts.local.

```
