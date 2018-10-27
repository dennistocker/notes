# Telegram Bot API

## Authority your bot

`684167756:AAEfb7JnIba1tGn6nkw7m8zpjvkMIVbNGt8`
`693741289:AAFl2P5fSstZ-bFW6bItUf1k_G9bGLAxfis`

## Making reqeusts

`https://api.telegram.org/bot<token>/METHOD_NAME`

We support **GET** and **POST** methods. We support four ways of passing parameters in Bot API reqeusts:

* URL query string
* application/x-www-form-urlencoded
* application/json(except for uploading files)
* multipart/form-data(used to upload files)

The resposne contains a JSON object.

## Available methods

### getUpdates

Use this method to receive incoming updates using long polling.

### getMe

A simple method for testing your bot's auth token.

### sendMessage
