# Prisma 1 notes

Now that prisma 2 is out, and if installed all commands for prisma need to be `prisma1` instead of `prisma`.

## Start Prisma Server on Heroku
Go to https://app.prisma.io and log in
Click on Servers at the top
Click Add Server
Run through steps

## Setting Up the Project
Create a folder and run `prisma1 init` and choose the server you just created.

Best way I've only figured out how to get the url if you've already created a project connecting to a local docker container is to make a new folder and run `prisma1 init`, then open the prisma.yml file afterwards to get the endpoint.
