# nginx-ssl-client

Using nginx to create simple test server for client cert authentication.

## Steps

1. Check certs/README.md to know how to create certs.
(opt). Set nginx/ssl-client.conf to `ssl_verify_client optional;` to test if server works.
2. Start nginx with `docker-compose up`.
3. Visit https://localhost:8443/ with your browser - you should get 400 Bad Request - No required SSL certificate was sent.
4. Next step would be to import client.p12, but it does not work. Check certs/README.md



