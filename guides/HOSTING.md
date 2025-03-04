# Plandex self-hosting 🏠

## Get started

The Plandex server requires a PostgreSQL database (ideally v14), a persistent file system, git, and these environment variables:

```bash
export DATABASE_URL=postgres://user:password@host:5432/plandex # replace with your own database URL
export GOENV=production
export SMTP_HOST=smtp.example.com
export SMTP_PORT=587
export SMTP_USER=user
export SMTP_PASSWORD=password
```

The server listens on port 8088 by default.

It can be run from a Dockerfile at `app/Dockerfile.server`:

```bash
git clone https://github.com/plandex-ai/plandex.git
VERSION=$(cat app/server/version.txt) # or use the version you want
git checkout server/v$VERSION
cd plandex/app
mkdir ~/plandex-server # or another directory where you want to store files
docker build -t plandex-server -f Dockerfile.server . 
docker run -p 8088:8088 \
  -v ~/plandex-server:/plandex-server \
  -e DATABASE_URL \
  -e GOENV \
  -e SMTP_HOST \
  -e SMTP_PORT \
  -e SMTP_USER \
  -e SMTP_PASSWORD \
  plandex-server
```

Or you can run it from source:

```bash
git clone https://github.com/plandex-ai/plandex.git
VERSION=$(cat app/server/version.txt) # or use the version you want
git checkout server/v$VERSION
cd plandex/app/server
export PLANDEX_BASE_DIR=~/plandex-server # or another directory where you want to store files
go run main.go
```

The server requires access to a persistent file system. If you're using Docker, it should be mounted to the container. In production, the `/plandex-server` directory is used by default as the base directory to read and write files. You can use the `PLANDEX_BASE_DIR` environment variable to change this.

In production, authentication emails are sent through SMTP. You can use a service like SendGrid or your own SMTP server.

## Development mode

If you set `export GOENV=development` instead of `production`:

- Authentication tokens will be copied to the clipboard instead of sent via email, and a system notification will pop up to let you know that the token is ready to paste. (SMTP environment variables are not required in development mode.)

- The default base directory will be `$HOME/plandex-server` instead of `/plandex-server`. It can still be overridden with `PLANDEX_BASE_DIR`.

## Health check

You can check if the server is running by sending a GET request to `/health`. If all is well, it will return a 200 status code.

## Create a new account

Once the server is running, you can create a new account by running `plandex sign-in` on your local machine.

```bash
plandex sign-in # follow the prompts to create a new account on your self-hosted server
```
