# How to run fast a very secure/insecure local concourse/vault with docker

## Basic ingredients

- docker-compose.yml
- concourse-policy.hcl

## Howto

```sh
# start the stack
docker-compose up -d
# enable authentication
export VAULT_ADDR='http://localhost:8200'
export VAULT_TOKEN=concourse
vault secrets enable -version=1 -path=concourse kv
vault policy write concourse ./concourse-policy.hcl
# set a little global "secret" variable
vault write concourse/main/a value=1
# login to concourse
fly -t docker login -c http://localhost:8080/ -utest -ptest
# set the simple pipeline
fly -t docker sp -p hello -c pipeline.yml
```

Run the [pipeline](http://localhost:8080/teams/main/pipelines/hello/) and you will see it printing out the value of `a`. That happens without exposing its value when doing `fly -t docker get-pipeline -p hello`.

Awesome!

## Pros

With this recipe you can run a Vault backed Concourse CI locally. 
That would potentially help:
- not publlishing any pipelines with exposed credentials
- familiarise yourself with vault and concourse without the pain of maintaining/running/paying a server somewhere

## Cons

- docker compose goes down, no vault persistency
- vault runs in dev mode (does not run in server/production mode duh!)