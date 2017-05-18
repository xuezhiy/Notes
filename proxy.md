
Install tsocks on Ubuntu and set server/port
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
