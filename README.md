
1. Update docker-compose following [their directions][install-compose] (update
   the version number as appropriate).

   ```
   sudo curl -L https://github.com/docker/compose/releases/download/1.20.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   sudo curl -L https://raw.githubusercontent.com/docker/compose/1.20.1/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
   ```
2. Create an acme.json file that's owned by root and chmod'ed 600.

3. Update the hostname references in `docker-compose.yml` and
   `traefik.toml`.

   If none of your frontend rules mention use a `Host` rule
   then uncomment and touch up the `acme.domains` bit at the bottom
   so that a certificate is generated.

4. Run docker-compose, e.g.

   ```
   docker-compose up -d
   ```

5. Cocktail.

6. ps.  The password for the admin user was generated like so:

   ```
   htpasswd -nb admin secure_password
   ```

   and then set in the `traefik.toml` file.

At this point:

| URL                    | result                                                                                                                                   |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| http://hostname/ping   | routed to Traefik's ping backend, respondes OK (or not).                                                                                 |
| http://hostname/api    | routes to Traefik's https port, which requires auth, then to the API/dashboard backend.                                                  |
| http://hostname/whoami | routes to Traefik's https port, which requires auth, then to a container running the "whoami" image, responds with some non-random data. |


[install-compose]: https://docs.docker.com/compose/install/#install-compose
