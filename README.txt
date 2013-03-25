Nginx Reverse Proxy access controlled by Apigee Gateway
-------------------------------------------------------

This is an Nginx reverse proxy configuration that uses a Lua script to check
an Apigee Gateway URL before allowing the proxy url to be accessed.

This will allow a local Nginx proxy to still use Apigee key management, rate
limiting, and analytics without having the traffic proxied through the Apigee
gateway. Useful in situations where one wants to control internal API traffic
using Apigee's cloud services.


How it works:

1. Requests to Nginx are forwarded to the Apigee Gateway with all original
   request headers included but without any body contents

2. The Gateway sees a normal request and acts on it, applying the appropriate
   policies based on configuration and resource paths

3. If the Gateway returns an error status code to Nginx, Nginx ends the
   request, and returns to the caller all the content and headers the Gateway
   returned to it

4. If no error is returned by the Gateway, the Nginx script appends the
   appropriate headers returned by the Gateway to the Nginx response and then
   calls the destination API server.  The caller gets the API server's
   response along with any headers sent by the Gateway.


Usage:

1. Change the indicated destination API server URL and the gateway API URL in
   the gw.conf file.

2. Set up appropriate policies for your gateway API.  For example, if your
   gateway API url was:

     http://apigee-ed-test.apigee.net/agent-endpoint

   then requests to:

     http://localhost:8081/my/resource

   would cause a request to be made (minus any request body content) to:

     http://apigee-ed-test.apigee.net/agent-endpoint/my/resource

   So, if you wanted to set a custom header for that resource, you'd go into
   the Gateway admin portal and for the API named "agent-endpoint", in the
   organization "apigee-ed", you'd set up a resource called "my/resource" and
   configure a policy to return that header.

   Any status codes other than 200 returned by Gateway policies will cause
   Nginx to interrupt execution of the request, so policies such as OAuth
   can protect access to Nginx-proxied API resources in effectively the
   same fashion they currently do.

   In normal usage, your API (for example, "agent-endpoint"), will not have
   a target configured for it, since the proxying of the target destination
   is handled in Nginx.

3. Either include the gw.conf from your nginx.conf or start nginx with the -c
   argument pointing to gw.conf.  For example, for Nginx:

     nginx -c `pwd`/gw.conf

   If using the Open Resty build of Nginx, use that instead:

     openresty -c `pwd`/gw.conf


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

