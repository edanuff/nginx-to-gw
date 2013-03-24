Nginx Reverse Proxy access controlled by Apigee Gateway
-------------------------------------------------------

This is an Ngina reverse proxy configuration that uses a Lua script to check
an Apigee Gateway URL before allowing the proxy url to be accessed.

This will allow a local Nginx proxy to still use Apigee key management, rate
limiting, and analytics without having the traffic proxied through the Apigee
gateway. Useful in situations where one wants to control internal API traffic
using Apigee's cloud services.

Usage:

Change the indicated destination API server URL and the gateway API URL in the
gw.conf file.

Either include the gw.conf from your nginx.conf or start nginx with the -c
argument pointing to gw.conf

Requirements:

Nginx with HttpLuaModule or OpenResty (recommended)


For OSX users new to Nginx, here's the quickstart:

1. Install homebrew

2. Install Lua

   brew install lua

3. Use homebrew to install openresty:

   brew tap nathantsoi/homebrew-openresty
   brew install open-resty

4. Clone the repo locally and then go to the repo directory and use the start
   script:

   git clone https://github.com/edanuff/nginx-to-gw.git
   cd nginx-to-gw
   ./bin/start.sh

   The proxy will be listening on localhost:8081

5. To stop the server, use the stop script:

   ./bin/start.sh

6. To reload the server conf, use the reload script:

   ./bin/reload.sh

The start, stop, and reload scripts in bin/ are provided for convenience when
using Open Resty and have no other importance.

Open Resty OSX install instructions borrowed from:

http://nathan.vertile.com/open-resty-the-prebuilt-reverse-proxy/

