nsqdelay - a delayed message queue for NSQ
==========================================

[![Build Status](https://travis-ci.org/bsphere/nsqdelay.svg?branch=master)](https://travis-ci.org/bsphere/nsqdelay)

__nsqdelay__ can be used for sending delayed messages on top of NSQ,
it listens on the __delayed__ topic by default (configurable) and receives JSON encoded messages with the following structure:

```json
{
  "topic": "my_topic",
  "body": "message_body",
  "send_at": 1234567890
}
```

It persists the messages to an SQLite file database and publishes them when the time comes.

## Features

- Delayed message delivery based on Unix timestamps
- SQLite persistence for reliability
- Automatic retry for failed message publishing
- Configurable delayed topic name
- Docker and Docker Compose support

## Usage

### Docker

For all command line arguments use:
```bash
docker run --rm gbenhaim/nsqdelay -h
```

Run with Docker:
```bash
docker run -d --name nsqdelay \
  -v /path/to/data:/data \
  gbenhaim/nsqdelay \
  -lookupd_http_address=http://nsqlookupd:4161 \
  -nsqd_tcp_address=nsqd:4150
```

### Docker Compose

A `docker-compose.yml` file is included for easy setup with NSQ:

```bash
docker-compose up -d
```

This will start:
- nsqlookupd (ports 4160-4161)
- nsqd (ports 4150-4151)
- nsqdelay (connected to the NSQ cluster)

### Command Line Options

- `-lookupd_http_address`: NSQ lookupd HTTP address (default: "http://127.0.0.1:4161")
- `-nsqd_tcp_address`: NSQ daemon TCP address (default: "127.0.0.1:4150")
- `-topic`: Topic to subscribe for delayed messages (default: "delayed")
- `-db_path`: Path to SQLite database file (default: "/data/nsqdelay.db")

## Building from Source

```bash
# Clone the repository
git clone https://github.com/bsphere/nsqdelay.git
cd nsqdelay

# Build with Go modules
go build -o nsqdelay .

# Or build with Docker
docker build -t nsqdelay .
```

## Important Notes

- Mount the `/data` volume to persist delayed messages across container restarts
- The SQLite database file is stored at `/data/nsqdelay.db` by default
- Messages are automatically retried if publishing fails
