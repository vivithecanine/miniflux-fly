This config sets up a basic Fly App with Miniflux

[Sign up on the fly.io website](https://fly.io/app/sign-in)

[Download the fly cli](https://github.com/superfly/flyctl)

Next, login to the CLI

```
fly auth login
```

Create an organization

```
fly orgs create miniflux
```

Attach a credit card or buy credits and assign them to the organization from the Fly Billing UI

Now we need to create the postgresql cluster

```
fly postgres create --initial-cluster-size 2 --vm-size shared-cpu-1x --volume-size 1
```

Skip app name creation, select the org you created, and set the region

The output will look like this

```
? App Name: <leave this blank>
? Select organization: miniflux-213
? Select region: sea (Seattle, Washington (US))
Creating postgres cluster  in organization personal
Postgres cluster cool-water-6661 created
  Username:    postgres
  Password:    e57ec41a67191c8329c5358e1826b326d1b993f71371526f
  Hostname:    cool-water-6661.internal
  Proxy Port:  5432
  PG Port: 5433
Save your credentials in a secure place, you won't be able to see them again!

Monitoring Deployment

2 desired, 2 placed, 2 healthy, 0 unhealthy [health checks: 6 total, 6 passing]
--> v0 deployed successfully

Connect to postgres
Any app within the personal organization can connect to postgres using the above credentials and the hostname "cool-water-6661.internal."
For example: postgres://postgres:e57ec41a67191c8329c5358e1826b326d1b993f71371526f@cool-water-6661.internal:5432
```

We'll use this data for setting up the app

Then make a new directory and do a `fly create`, you may specify whatever name you wish, or leave it blank to have fly generate one

Great, peek at the fly.toml and note your app name, and let's deploy the actual app

Now we need to set some secrets, let's populate the database string (set your own values)

```
echo "postgres://postgres:e57ec41a67191c8329c5358e1826b326d1b993f71371526f@cool-water-6661.internal/miniflux?sslmode=disable" | flyctl secrets set DATABASE_URL=-
```

Now let's set the admin username and password, set something secure here

```
echo "admin" | flyctl secrets set ADMIN_USERNAME=-
echo "hunter2" | flyctl secrets set ADMIN_PASSWORD=-
```

Clone this repo

```
git clone https://github.com/vivithecanine/miniflux-fly.git
```

Now edit the fly.toml in this repo and change your app name on line 1

```
app = "long-star-5369"
```

Then change to the directory where miniflux-fly is (if you aren't in it already)

```
fly deploy --remote-only

# Then when we're done clean up our secrets
fly secrets unset ADMIN_USERNAME
fly secrets unset ADMIN_PASSWORD
```

Now you can login at `https://<yourappname>.fly.dev/`

Should you ever want to destroy your organization, you can use `fly orgs delete` to clean up after yourself
