

## Connect to NPM Web-Interface
On Windows the following command allows the connection to the NPM Interface on the local host:
ssh -i <User-Home>\.ssh\id_ed25519 -N -L 8181:127.0.0.1:81 rocky@<vm-IP>
Open http://localhost:8181 in the browser while the terminal remains active
Set initial nginx password and user account

## Configure DNS entries
At the DNS Provider, create A Records linking to the VMs static IPv4 for the following hosts:
    explorer
    wv-db
    @

## Configure Reverse Proxy and Authentication for WV-See
Create access list: 
    configure username and password under authorizations
    set allow rules to 0.0.0.0/0
Under Hosts create Proxy Hosts:
    create a proxy host for wvsee:
        set domain name as explorer.<public domain> 
        set forward to "wvsee" on port 3000 with HTTP
        select the previously created access list to enable basic authentication
        under SSL request a new certificate from Let's Encrypt
    create a proxy for the weaviate database:
        set domain name as wv-db.<public domain> 
        set forward to "weaviate" on port 8080 with 
        select "publicly accessible" under access list
        under SSL request a new certificate from Let's Encrypt
        enable HTTP/2 Support
        under settings (wheel) paste:
            underscores_in_headers on;
            location / {
                grpc_read_timeout 300s;
                grpc_send_timeout 300s;
                grpc_socket_keepalive on;
                if ($http_content_type = "application/grpc") {
                    grpc_pass grpc://weaviate:50051;
                }
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection $http_connection;
                proxy_http_version 1.1;
                include conf.d/include/proxy.conf;
            }