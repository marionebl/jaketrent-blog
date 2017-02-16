
## Run Locally

```
hugo server
open http://localhost:1313
```

## Build Locally

```
hugo 
npm install -g http-server
cd public && http-server -p 1313 .
open http://localhost:1313
```

## Create Beta Environment

```
heroku create --remote heroku-beta --buildpack https://github.com/roperzh/heroku-buildpack-hugo.git jaketrent-blog-beta
```

## Deploy to Beta

```
git push heroku-beta master
```

## Deploy to Prod

```
heroku config:add BUILDPACK_URL="https://github.com/roperzh/heroku-buildpack-hugo.git"
git push heroku master
```
