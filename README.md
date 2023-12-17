# Tech Radar

This project is a fork of [Thoughtworks Tech Radar](https://github.com/thoughtworks/build-your-own-radar), which is used to visualize your organization's technological stack.

This fork significantly simplifies the original project's layout and only uses the JSON configuration.

## Run using a pre-built Docker image (recommended)

This project automatically builds and publishes a Docker image on every merge, so if you just want to run the app, you don't have to mess with the source code at all.

Given this `docker-compose.yml` config:

```yaml
version: "3.8"
services:
  tech_radar:
    image: ghcr.io/awthwathje/build-your-own-radar
    ports:
      - 8080:80
    volumes:
      - ./radar.json:/opt/build-your-own-radar/data/radar.json
```

Then issuing `docker-compose up` command will run the app on `8080` port. Make sure `./radar.json` is available. It will be served by the included Nginx and is ready to be deployed to production.

## Build it yourself

### Docker with included Nginx

If you prefer to build your own Docker image, that's also possible. Clone the repo and create a `docker-compose.yml` config similar to this:

```yaml
version: "3.8"
services:
  tech_radar:
    build: .
    ports:
      - 8080:80
    volumes:
      - ./radar.json:/opt/build-your-own-radar/data/radar.json
```

Running `docker-compose up` will make the app available at <http://localhost:8080>.

### I don't need Nginx, I need static files

In scenarios where you only need the generated files, and not the whole app with Nginx, you can adjust your Docker run configuration and grab the files to upload to your hosting.

Example config to deploy to GitLab Pages:

`docker-compose.yml`:

```yaml
version: "3.8"
services:
  tech_radar:
    image: ghcr.io/awthwathje/build-your-own-radar
    ports:
      - 3939:80
    volumes:
      - ./radar.json:/opt/build-your-own-radar/data/radar.json
```

`.gitlab-ci.yml`:

```yaml
pages:
  stage: deploy
  image: docker
  script:
    - docker-compose run --name tech-radar tech_radar /bin/sh -c 'npm run build:prod'
    - docker cp tech-radar:/opt/build-your-own-radar/dist ./public
  after_script:
    - docker-compose down
  artifacts:
    paths:
      - public
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
  tags:
    - runner-small
```

### Local development

Assuming Node.js is available locally:

- `npm install`
- `npm run dev`

The dev version will be available at <http://localhost:8080>.

## Example radar.json

This project comes with an example `radar.json` in `data` directory. You need to update it using your own data. Here is the example:

```json
{
  "title": "Company Name's Tech Radar",
  "quadrants": [
    "Languages & Frameworks",
    "Tools",
    "Platforms",
    "Techniques"
  ],
  "rings": ["Adopt", "Trial", "Assess", "Hold"],
  "blips": [
    {
      "name": "React",
      "quadrant": "Languages & Frameworks",
      "ring": "adopt",
      "isNew": "FALSE",
      "description": "React is used for building UI components."
    },
    {
      "name": "Rust",
      "quadrant": "Languages & Frameworks",
      "ring": "adopt",
      "isNew": "FALSE",
      "description": "Rust is used for building CLI tools, system apps and backend services."
    },
    {
      "name": "VS Code",
      "quadrant": "Tools",
      "ring": "adopt",
      "isNew": "TRUE",
      "description": "VS Code is used as an integrated development environment."
    },
    {
      "name": "Snowflake",
      "quadrant": "Tools",
      "ring": "adopt",
      "isNew": "FALSE",
      "description": "Snowflake is as a data warehouse."
    },
    {
      "name": "AWS",
      "quadrant": "Platforms",
      "ring": "adopt",
      "isNew": "FALSE",
      "description": "A public cloud platform."
    },
    {
      "name": "GitHub",
      "quadrant": "Platforms",
      "ring": "adopt",
      "isNew": "FALSE",
      "description": "A Git repository hosting service and a social network for developers."
    },
    {
      "name": "BDD",
      "quadrant": "Techniques",
      "ring": "adopt",
      "isNew": "FALSE",
      "description": "Behaviod-Driven Design"
    },
    {
      "name": "TBD",
      "quadrant": "Techniques",
      "ring": "adopt",
      "isNew": "FALSE",
      "description": "Trunk-Based Development"
    }
  ]
}
```
