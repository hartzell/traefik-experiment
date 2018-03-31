My implementation of a reverse proxy and a set of containers.  Based
on various internet resources, such as the [Digital Ocean Ubuntu
example][do-example] and the [configuration
fragments][traefik-examples] in the Traefik docs.


1. Update docker-compose following [their directions][install-compose] (update
   the version number as appropriate).

   ```
   sudo curl -L https://github.com/docker/compose/releases/download/1.20.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   sudo curl -L https://raw.githubusercontent.com/docker/compose/1.20.1/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
   ```
   
2. Create an acme.json file that's owned by root and chmod'ed 600.

3. Update the hostname references and other variable bits (e.g. the
   paths to the files that are mounted into the Docker containers) in
   `docker-compose.yml` and `traefik.toml`.

   If none of your frontend rules mention use a `Host` rule
   then uncomment and touch up the `acme.domains` bit at the bottom
   so that a certificate is generated.
   
4. Create (if it doesn't already exist) the Docker network you'll be
   using, e.g.:
   
   ```
   docker network create web
   ```

5. Run docker-compose, e.g.

   ```
   docker-compose up -d
   ```

6. Cocktail.

7. ps.  The password for the admin user was generated like so:

   ```
   htpasswd -nb admin secure_password
   ```

   and then set in the `traefik.toml` file.

8. [ ] pps.  You should probably change it if/when you use this for
   real...

At this point:

| URL                     | result                                                                                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| http://hostname/ping    | routed to Traefik's ping backend, responds OK (or not).                                                                                    |
| http://hostname/api     | redirects to Traefik's https port, which requires auth, then to the API/dashboard backend.                                                  |
| https://hostname/api    | hits Traefik's https port, which requires auth, then to the API/dashboard backend.                                                          |
| http://hostname/whoami  | redirects to Traefik's https port, which requires auth, then to a container running the "whoami" image, responds with some non-random data. |
| https://hostname/whoami | hits Traefik's https port, which requires auth, then to a container running the "whoami" image, responds with some non-random data.         |

[install-compose]: https://docs.docker.com/compose/install/#install-compose
[do-example]: https://www.digitalocean.com/community/tutorials/how-to-use-traefik-as-a-reverse-proxy-for-docker-containers-on-ubuntu-16-04
[traefik-examples]: https://docs.traefik.io/user-guide/examples/
