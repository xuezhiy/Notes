1. Use socks5 as http proxy

  a. Install shadowsocks on remote server(ip is IP_REMOTE)
  
     # apt-get install -y python-pip
     # pip install shadowsocks
     
  b. Start shadowsocks on remote server
  
     Add /etc/shadowsocks.json file
     
     {
        "port_password":
        {
           "8388":"secret1",
           "8389":"secret2"
        },
        "timeout": 300,
        "method":"aes-256-cfb",
        "fast_open": false
     }
     
     Start the ssserver:
     # sudo ssserver -c /etc/shadowsocks.json -d start

  c. Install shadowsocks on local server
  
     # apt-get install -y python-pip
     # pip install shadowsocks
     
  d. Start shadowsocks on local server
  
     Add /etc/shadowsocks.json file
     
     {
        "server":"IP_REMOTE",
        "server_port":8388,
        "password":"secret1",
        "local_port":1080,
        "timeout":300,
        "method":"aes-256-cfb"
     }
     
     # sslocal -c /etc/shadowsocks.json -d start
  
  e. Now the local server can connect to remote server by socks5 proxy
  
     # export http_proxy=socks5://127.0.0.1:1080
     # curl www.google.com          
     
  f. For unsupport socks5 proxy, we can use privoxy
  
     # apt-get install -y privoxy
     
     Add below content to /etc/privoxy/config
     forward-socks5   /               127.0.0.1:1080  .
     Start privoxy process
     # systemctl start privoxy
     # export http_proxy=http://127.0.0.1:8118
     # ss -antp |grep 8118
      LISTEN     0      128    127.0.0.1:8118                     *:*                   users:(("privoxy",pid=17712,fd=4))
     # ss -antp |grep 1080
      LISTEN     0      128    127.0.0.1:1080                     *:*                   users:(("sslocal",pid=17705,fd=4))
   
     Now we can use both http_proxy=http://127.0.0.1:8118 and socks5://127.0.0.1:1080

   g. Use the http proxy in docker
   
      Add or edit file /etc/systemd/system/docker.service.d/http_proxy.conf
      
      [Service]
        Environment=HTTP_PROXY=http://127.0.0.1:8118/
        Environment=HTTPS_PROXY=http://127.0.0.1:8118/
        Environment=NO_PROXY=localhost,127.0.0.1

      # systemctl daemon-reload
      # systemctl restart docker
      # systemctl show --property=Environment docker       // Show the docker environment
      # docker pull gcr.io/google_containers/kube-apiserver-amd64:v1.6.0    // should be work.


2.Install tsocks on Ubuntu and set server/port

    # apt-get install tsocks
    # vi /etc/tsocks.conf

    // add server and port to the config
    // Use proxy to execute
    # sudo tscoks apt-get update

Install tsocks on Mac

tap repo: brew tap Anakros/homebrew-tsocks
install tsocks: brew install --HEAD tsocks
set up socks proxy: ssh -D 5555 server
edit configuration file: vim /usr/local/etc/tsocks.conf
set server as localhost: server = localhost
set server port as 5555: server_port = 5555
check that everything works: tsocks curl ifconfig.me
