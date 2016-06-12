# Our Distributed Docker Registry Setup

Our product uses a few dozen container types over many Swarm
hosts. Performing a "pull" on the whole network of Swarm hosts causes
them to all pull at once, placing a fair bit of load on a registry.

We required a private registry which could handle this kind of load,
plus offer presence in several AZ's, plus be a little redundant.

Our solution was in EC2 but wasn't tied to it.

1. Create a pile of registry servers at multiple locations, say East and West. We
   used three m4.large at each site but after looking at the negligible load, we
   probably wanted more, smaller boxes.
2. Configure each registry server to use a shared global storage (S3) backend
   driver, which is eventually consistent.  An image push to any should result in
   all having the image.
3. Create an SSL certificate.  We used https://letsencrypt.org for this.
4. Create a load balancer for each site accepting TCP/443 and presenting the
   cert. Note each LB has an internal DNS name in EC2.
5. Add the servers to their balancers.
6. Advertise an external DNS name (say `example.com`) as an alias for each LB as above.
7. Clients should now `docker login example.com` and should be able to pull images
   like `example.com/apps/busybox`.

Finally, we use [Docker Registry Diff](README.md) to assess the consistency of this operation.

