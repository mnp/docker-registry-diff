# Docker Registry Diff

Given several Docker registries and credentials, this tool will examine their
catalogs, tags, and manifests to assure the same images are available on all
registries.  By default, it simply exits 0 if everything matches, otherwise
printing differences and exiting nonzero: suitable for use in cron job.

## Usage

usage: docker-registry-diff [-h] [--catalog] [--verbose] --username USERNAME --password PASSWORD registries [registries ...]

Compare several Docker registries

positional arguments:
  registries           Hostnames of registries

optional arguments:
  -h, --help           show this help message and exit
  --catalog            Don't diff, just dump a catalog of names and tags from the first registry given
  --verbose            Print status updates, otherwise be silent like "diff"
  --username USERNAME  Registry username
  --password PASSWORD  Registry password

## Example

```
$ ./docker-registry-diff --verbose --username joedev --password 1234xyzpdq vegas.myhost.com jersey.myhost.com
checking catalogs: ..........................................................................ok
checking tags: ok
checking manifests: ......................................................................................................................................................................................................................................................................................................................................................ok
finished: okay
```

## Motivation

Running a private Docker registry gets more interesting if you want your registry
to be redundant and distributed.  If you have a number of simultaneous clients
pulling images all at once (hello, Swarm), you might also scale to more registries.

From a monitoring standpoint, you probably want to make sure all registries are
serving the same images shortly after a push is done to any registry. This tool
offers that assurance.  See [Our Distributed Docker Registry Setup](our-setup.md).

## Authentication

Only basicauth is supported at the moment: `--username` and `--password` are given
on the command line.  SSL is TODO.

Note also that because of our setup (see below), we need to give internal server or LB addresses, which 

## References
* https://docs.docker.com/registry/spec/api
