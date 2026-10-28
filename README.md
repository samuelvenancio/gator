# Gator

Gator is a lightweight Go CLI for managing RSS feeds and browsing recent posts from the feeds you follow. It stores users, feeds, follow relationships, and fetched posts in PostgreSQL.

## Requirements

Before running Gator, make sure you have:

- Go installed
- PostgreSQL installed and running

You can install Go from the official Go website, and PostgreSQL from your operating system package manager or the official PostgreSQL distribution.

## Install the CLI

If you want to install the compiled `gator` binary globally, run:

```bash
go install github.com/samuelvenancio/gator@latest
```

If `$(go env GOPATH)/bin` is on your `PATH`, you can then run:

```bash
gator --help
```

If you are working from a local checkout instead of installing from GitHub, you can also build it locally:

```bash
go build .
```

> Remember: `go run` is for development. `go build` and `go install` produce a standalone binary that you can run without the Go toolchain installed.

## Set up PostgreSQL

Create a database for the app:

```bash
createdb gator
```

Then apply the schema files in order from `sql/schema`:

```bash
psql -d gator -f sql/schema/001_users.sql
psql -d gator -f sql/schema/002_feeds.sql
psql -d gator -f sql/schema/003_feed_follows.sql
psql -d gator -f sql/schema/004_add_last_fetched_at_feeds.sql
psql -d gator -f sql/schema/005_posts.sql
```

## Configure Gator

Gator reads its config from `~/.gatorconfig.json`.

Create the file with a PostgreSQL connection string like this:

```json
{
  "db_url": "postgres://postgres:postgres@localhost:5432/gator?sslmode=disable",
  "current_user_name": ""
}
```

If you use a different username, database name, or port, update `db_url` accordingly.

## Use the CLI

After the database and config are set up, you can start creating users and feeds.

### Common commands

```bash
gator register alice
gator login alice
gator users
gator addfeed hackernews "https://hnrss.org/frontpage"
gator feeds
gator follow "https://hnrss.org/frontpage"
gator following
gator browse
gator agg 1m
```

### What the commands do

- `register <name>`: create a new user and set them as the current user
- `login <name>`: switch the active user
- `users`: list all users and mark the active one
- `addfeed <name> <url>`: create a feed and follow it for the active user
- `feeds`: list all feeds in the database
- `follow <feed_url>`: follow an existing feed for the active user
- `following`: show the feeds the active user is following
- `browse`: display recent posts for the active user
- `agg <duration>`: continuously fetch feeds on a timer, such as `30s` or `1m`

## Example workflow

```bash
# Create a user
gator register alice

# Add a feed
gator addfeed technews "https://example.com/feed.xml"

# See the feed list
gator feeds

# Browse recent posts
gator browse

# Start the aggregator
gator agg 1m
```

## Development notes

This project uses the default Go standard library and PostgreSQL via `lib/pq`. The schema files under `sql/schema` are the source of truth for the database structure.

## Publishing to GitHub

When you are ready to share the project, push it to a public Git repository and submit the repository URL. The app is ready to be built and installed as a normal Go binary once the database and config are in place.
